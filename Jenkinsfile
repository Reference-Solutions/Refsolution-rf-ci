pipeline {
    agent { 
		label 'windows-lab-pc'
	}
    stages {
        stage('Read manifest file') {
            steps {
                script {
                    // git branch: 'master', credentialsId: 'Soco-credentials-hari', url: 'https://sourcecode.socialcoding.bosch.com/scm/~pow2kor/refsolution-rf-ci.git'
					def customWorkspace = env.CUSTOM_WORKSPACE
                    echo "Custom Workspace: ${customWorkspace}"
                    manifest_file_path = "${customWorkspace}//manifest.xml"
                    def parsedManifest = readManifest(manifest_file_path)
					println parsedManifest

                }
            }
        }
    }
}

def readManifest(String manifestFilePath) {
    def xml = readFile encoding: 'UTF-8', file: manifestFilePath
    def parsedManifest = new XmlSlurper().parseText(xml)
    return parsedManifest
}
