pipeline {
    agent { 
		label 'windows-lab-pc'
	}
    stages {
        stage('Read manifest file') {
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
	println componentContent["robotFrameworkDir"].text()
	println componentContent["testSuiteName"].text()
    
    def robot_options = "--outputdir reports " +
                        "--consolecolors on"

    def robot_test_dir = env.ROBOT_TEST_DIR  // Update with your test directory


    script.bat """
        echo 'RF execution starts'
        python -m robot.run ${robot_options} ${robot_test_dir}
    """
    publishHTML()
}

def publishHTML() {
    
    script.publishHTML(target: [
        allowMissing: false,
        alwaysLinkToLastBuild: false,
        keepAll: true,
        reportDir: 'reports',
        reportFiles: 'report.html', // Modify this to match your report file
        reportName: 'Robot Framework Report',
        reportTitles: 'Robot Framework Report',
        wrapperName: 'htmlpublisher'
    ])
}

