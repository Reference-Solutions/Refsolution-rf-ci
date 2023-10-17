manifest_file_path = 'refsolution-rf-ci/manifest.xml'

pipeline {
    agent { 
		label 'windows-lab-pc'
	}
    stages {
        stage('Read manifest file') {
            steps {
                script {
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

