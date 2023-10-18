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
                    println manifest_file_path
                    def parsedManifest = readManifest(manifest_file_path)
					println parsedManifest

                    def repoUrl = manifestContent.parameters.parameter.find { it.@name == "repoUrl" }?.@value
                    def branchName = manifestContent.parameters.parameter.find { it.@name == "branchName" }?.@value
                    def credentials = manifestContent.parameters.parameter.find { it.@name == "Credentials" }?.@value
                    println "RobotframeworkContent: ${repoUrl} ${branchName}${credentials}"

                }
            }
        }
    }
}

def readManifest(String manifest_file_path) {
    def xml = readFile encoding: 'UTF-8', file: manifest_file_path
    def parsedManifest = new XmlSlurper().parseText(xml)
    return parsedManifest
}

