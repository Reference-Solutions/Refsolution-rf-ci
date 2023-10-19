pipeline {
    agent { 
		label 'windows-lab-pc'
	}
    stages {
        stage('Read manifest file and execute rf') {
            steps {
                script{
                 
                    String workspaceName_New = env.EXECUTOR_WORKSPACE
                    println workspaceName_New

                    customWorkspace = env.CUSTOM_WORKSPACE

                    manifest_file_path = "${customWorkspace}\\${workspaceName_New}\\manifest.xml"
                    
					parsed_manifest = readManifest(manifest_file_path)
					
					for (component in parsed_manifest['component']){
						println "Component Name : $component"
						checkoutRobotRepo(component)
						executeRobot(component)
					}
                }
            }
        }
    }

    post {
        always {
			publishRobotResult("testdata", 100, 95)
      }
}

}

def readManifest(String manifest_file_path) {
	def xml = readFile(encoding: 'UTF-8', file: manifest_file_path)
	parsed_manifest = new XmlParser().parseText(xml)
	return parsed_manifest
}

def checkoutRobotRepo(def rfContent){
	
	url = rfContent["repoUrl"].text()
	branch = rfContent["branchName"].text()
	credentialsId = rfContent["credentials"].text()
	println "$url, $branch, $credentialsId"
	
	doCheckout(url, branch, credentialsId, [])	
}

def executeRobot(def rfContent){
	String testSuiteDir = rfContent["testSuiteDir"].text()
	
	String testSuiteName = rfContent["testSuiteName"].text()

    String testSuiteFullPath = "${testSuiteDir}\\${testSuiteName}"  // Update with your test directory

	String robotCmd = "python -m robot"
	
	robotCmd = robotCmd + " --outputdir testdata"
	// robotCmd = robotCmd + " --tag"
	// robotCmd = robotCmd + " --variable"
	robotCmd = robotCmd + " --consolecolors on"
	robotCmd = robotCmd + " --report ${testSuiteName}_REPORT"
	robotCmd = robotCmd + " --log ${testSuiteName}_LOGS"
	robotCmd = robotCmd + " --output ${testSuiteName}_OUTPUT "
	// robotCmd = robotCmd + " --loglevel level"
	// robotCmd = robotCmd + " --include tag "
	// robotCmd = robotCmd + " --exclude tag "
	
	//This testSuiteFullPath variable need to be added at last in the robotCmd
	robotCmd = robotCmd + " ${testSuiteFullPath}"
	
	runCmd(robotCmd)
}

def runCmd(String cmd){
	if (isUnix()){
		sh cmd
	}
	else{
		bat cmd
	}
}

def publishRobotResult(String testResultsDirectory, Integer passThreshold, Integer unstableThreshold) {
	stage('Publish test results') {
		step([
				$class           : 'hudson.plugins.robot.RobotPublisher',
				outputPath       : "${testResultsDirectory}",
				passThreshold    : passThreshold,
				unstableThreshold: unstableThreshold,
				otherFiles       : '*.png',
				reportFileName   : '**\\*REPORT*.html',
				logFileName      : '**\\*LOGS*.html',
				outputFileName   : '**\\*OUTPUT*.xml'
		])
	}
}


def doCheckout(def url, def branch, def credentialsId, def extensions){
	boolean customCheckout = url != null
	
	checkout([
		$class: 'GitSCM',
		branches: customCheckout ? [[name: '*/' + branch]] : scm.branches,
		extensions: (customCheckout || !scm.extensions) ? extensions : scm.extensions + extensions,
		userRemoteConfigs: customCheckout ? [[url: url, credentialsId: credentialsId]] : scm.userRemoteConfigs
	])
}