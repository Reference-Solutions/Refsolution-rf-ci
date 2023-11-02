pipeline {
    agent {
        label "sree-apertis"
    }

    environment {
        PAT_TOKEN = credentials('zrd2kor-soco-hil')
        EC2_INSTANCE_ID = 'i-0054956e1b83a0a90'
    }

    stages {
		stage('Start the AWS Instance') {
            steps {
                script {
                    sh '''
                    aws ec2 start-instances --instance-ids ${EC2_INSTANCE_ID}
                    sleep 40
                    '''
                }
            }
        }
        stage('Checkout & Execute Robot Framework') {
            steps {
                script {
                    workspace = env.WORKSPACE
                    if (env.EXECUTOR_WORKSPACE != null)
                        workspace = env.EXECUTOR_WORKSPACE
                    parsed_manifest = readManifest(workspace + "\\" + pip_manifest_file_path)
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

                }
            }
        }
        stage('Copy testdata report to S3 Bucket') {
            steps {
                script {
                    sh '''
                    aws ssm start-session --document-name 'AWS-StartInteractiveCommand' --parameters '{"command": ["aws s3 cp /home/ssm-user/test-html-report/TestSuits/Example/testdata s3://hil-validation/testdata --recursive"]}' --target ${EC2_INSTANCE_ID}
                    '''
                }
            }
        }
        stage('Archive the Artifacts') {
            steps {
                script {
                    sh '''
                    aws s3 cp s3://hil-validation/testdata ./testdata --recursive
                    '''
                }
            }
        }
		stage('Stop the AWS Instance') {
            steps {
                script {
                    sh '''
                    aws ec2 stop-instances --instance-ids ${EC2_INSTANCE_ID}
                    '''
                }
            }
        }
    }
    post {
        always {
            publishRobotResult("testdata", 90, 80)
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
    stage('Publish test results') {
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