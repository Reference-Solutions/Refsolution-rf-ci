manifest_file_path = 'refsolution-rf-ci/manifest.xml'

pipeline {
    agent { 
		label 'windows-lab-pc'
	}
    stages {
        stage('Read manifest file') {
            steps {
                script {
					readManifest(manifest_file_path)
                }
            }
        }
    }
}

 

def readManifest(def manifest_file_path){
	println manifest_file_path
}

