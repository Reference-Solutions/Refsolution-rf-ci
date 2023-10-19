pipeline {
    agent { 
		label 'windows-lab-pc'
	}
    stages {
        stage('Read manifest file') {
            steps {
                script {
                   // git branch: 'master', credentialsId: 'Soco-credentials-hari', url: 'https://sourcecode.socialcoding.bosch.com/scm/~pow2kor/refsolution-rf-ci.git'
			
                    String workspaceName_New = env.EXECUTOR_WORKSPACE
                    println workspaceName_New


                  //  manifest_file_path = "C:\\Jenkins\\workspace\\common-Test\\SharedLib-Restructure\\Test\\manifest.xml"
                    manifest_file_path = "${workspaceName_New}\\manifest.xml"
                    println manifest_file_path
                    
                    def manifestContent = readManifest(manifest_file_path)
					println manifestContent


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





