pipeline {
    agent { 
		label 'windows-lab-pc'
	}
    stages {
        stage('Read manifest file') {
            steps {
                script {
                 
                    String workspaceName_New = env.EXECUTOR_WORKSPACE
                    println workspaceName_New

                    customWorkspace = env.CUSTOM_WORKSPACE

                    manifest_file_path = "${customWorkspace}\\${workspaceName_New}\\manifest.xml"
                    println manifest_file_path
                    
                    def manifestContent = readManifest(manifest_file_path)
					println manifestContent
                    
                    // Extract the repoUrl value
                   // def repoUrl = manifestContent.parameters.parameter.find { it.name.text() == " repoUrl:" }?.value.text()
    
                    def repoUrl = manifestContent.parameters.parameter.find { it.@name == " repoUrl:" }?.@value
                    println "RepoUrl: ${repoUrl}"


                }
            }
        }
    }
}



def readManifest(String manifest_file_path) {
    def xml = readFile encoding: 'UTF-8', file: manifest_file_path
    def manifestContent = new XmlSlurper().parseText(xml)
    return manifestContent
}


