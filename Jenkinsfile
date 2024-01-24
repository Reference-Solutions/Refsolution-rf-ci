pipeline {
    agent {
        label "sree-apertis"
    }

    environment {
        PAT_TOKEN = credentials('zrd2kor-soco-hil')
        EC2_INSTANCE_ID = 'i-0054956e1b83a0a90'
    }

    stages {
        stage('Run HiL Robot Framework') {
            steps {
                script {
                    sh '''
                    aws ec2 start-instances --instance-ids ${EC2_INSTANCE_ID}
                    sleep 40
                    '''
					String repoUrl = params.pip_robot_url ?: env.pip_robot_url
					String branch = params.pip_robot_branch ?: env.pip_robot_branch
					String credentialsId = params.pip_robot_checkout_credentials ?: env.pip_robot_checkout_credentials
					
					def repoName = repoUrl.replaceAll(".*/|.git","")
					
					ArrayList extensions = []
					extensions << [$class: 'RelativeTargetDirectory', relativeTargetDir: repoName]
					
					def manifestFile = params.pip_robot_manifest_file_path ?: env.pip_robot_manifest_file_path
				
                    doCheckout(repoUrl, branch, credentialsId, extensions)
					
                    parsed_manifest = readManifest(manifestFile)
                    
					if (!parsed_manifest) {
                        error "Failed to parse the manifest.xml file."
                    }
                    for (component in parsed_manifest['component']) {
                        println "Component Name : $component"
                        try {
                            checkoutGitRepo(component)
                            executeRobot(component)
                        } catch (Exception e) {
                            error "Failed to checkout component: $e"
                            
                        }
                        
                    }
                    sh '''
                    aws ssm start-session --document-name 'AWS-StartInteractiveCommand' --parameters '{"command": ["aws s3 cp /home/ssm-user/test-html-report/TestSuits/HiL_System/testdata s3://hil-validation/testdata --recursive && cd ~ && rm -rf test-html-report"]}' --target ${EC2_INSTANCE_ID}
                    aws s3 cp s3://hil-validation/testdata ./testdata --recursive
                    aws ec2 stop-instances --instance-ids ${EC2_INSTANCE_ID}

                    '''
                }
            }
        }
    }
}

def readManifest(String manifest_file_path) {
	def xml = readFile(encoding: 'UTF-8', file: manifest_file_path)
	parsed_manifest = new XmlParser().parseText(xml)
	return parsed_manifest
}

def executeSSMCommand(command) {
    def instanceId = 'i-0054956e1b83a0a90'
    def ssmCommand = """
    aws ssm start-session --document-name 'AWS-StartInteractiveCommand' --parameters '{"command": ["$command"]}' --target $instanceId
    """
    def result = sh(script: ssmCommand, returnStatus: true, returnStdout: true)
    if (result != 0) {
        def errorOutput = sh(script: ssmCommand, returnStatus: true, returnStdout: false, returnStderr: true)
        error "Failed to execute AWS SSM command. Exit code: ${result}, Error Output: ${errorOutput}"
    }
    return result.toString()
}

def checkoutGitRepo(def repoDetails) {
    url = repoDetails["repoUrl"].text()
	branch = repoDetails["branchName"].text()
	credentialsId = repoDetails["credentials"].text()
	user = repoDetails["username"].text()
	println "$url, $branch, $credentialsId, $user"

    String command = "git clone --recursive -b $branch https://$user:$PAT_TOKEN@$url"
    executeSSMCommand("cd ~ && $command")
}

def executeRobot(def repoDetails) {
    String testSuiteDir = repoDetails["testSuiteDir"].text()
	String testSuiteName = repoDetails["testSuiteName"].text()
    String testSuiteFullPath = "/home/ssm-user/test-html-report/${testSuiteDir}/${testSuiteName}"

    String command = "python3 -m robot"

    command = command + " --outputdir testdata"
	command = command + " --consolecolors on"
	command = command + " --report ${testSuiteName}_REPORT"
	command = command + " --log ${testSuiteName}_LOGS"
	command = command + " --output ${testSuiteName}_OUTPUT "
	command = command + " --loglevel TRACE:INFO"
    command = command + " ${testSuiteFullPath}"

    executeSSMCommand("cd $testSuiteFullPath && $command")
}

def publishRobotResult(String testResultsDirectory, Integer passThreshold, Integer unstableThreshold) {
    stage('Publish HiL_Report') {
		script {
			publishHTML([
            allowMissing: true,
            alwaysLinkToLastBuild: true,
            keepAll: true,
            reportDir: '',
            reportFiles: 'report.html',
            reportName: 'HTML Report',
            reportTitles: 'report.html'
			])
			robot disableArchiveOutput: true,
            logFileName: '**\\*LOGS*.html',
            outputFileName: '**\\*OUTPUT*.xml',
            passThreshold: passThreshold,
            reportFileName: '**\\*REPORT*.html',
            unstableThreshold: unstableThreshold,
			outputPath       : "${testResultsDirectory}",
			otherFiles       : ''
		}
	}
}

def doCheckout(String url=null, String branch=null, String credentialsId=null, ArrayList extensions=[]) {
	boolean customCheckout = url != null
	
	checkout([
		$class: 'GitSCM',
		branches: customCheckout ? [[name: '*/' + branch]] : scm.branches,
		extensions: (customCheckout || !scm.extensions) ? extensions : scm.extensions + extensions,
		userRemoteConfigs: customCheckout ? [[url: url, credentialsId: credentialsId]] : scm.userRemoteConfigs
	])
}
