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
			step([
				$class : "RobotPublisher",
				outputPath : "reports/",
				outputFileName : "*.xml",
				disableArchiveOutput : false,
				passThreshold : 100,
				unstableThreshold: 95.0,
				otherFiles : "*.png",
				frameOptions: [
                allowScripts: true // Add the 'allow-scripts' permission
            ]
			])
      }
}

}

def readManifest(String manifest_file_path) {
	def xml = readFile(encoding: 'UTF-8', file: manifest_file_path)
	parsed_manifest = new XmlParser().parseText(xml)
	return parsed_manifest
}

def checkoutRobotRepo(def componentContent){
	
	url = componentContent["repoUrl"].text()
	branch = componentContent["branchName"].text()
	credentialsId = componentContent["credentials"].text()
	println "$url, $branch, $credentialsId"
	
	doCheckout(url, branch, credentialsId, [])	
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

def executeRobot(def componentContent){
	rfDir = componentContent["robotFrameworkDir"].text()
	testSuite = componentContent["testSuiteName"].text()
    
    def robot_options = "--outputdir reports " +
                        "--consolecolors on"

    def robot_test_dir = "${rfDir}\\${testSuite}"  // Update with your test directory

    bat """
        echo 'RF execution starts'
        python -m robot.run ${robot_options} ${robot_test_dir}
    """
}

