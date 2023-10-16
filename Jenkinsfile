pipeline {
    agent any

    stages {
        stage('Read manifest file') {
            steps {
                script {
                    runTask(env, currentBuild.rawBuild)
                }
            }
        }
    }
}

def runTask(Map env, currentBuild) {
    echo "TODO: Verification"
    def manifestFilePath = currentBuild.getEnvVars()["MANIFEST_FILE_PATH"]
    echo "manifestFilePath: ${manifestFilePath}"

    def manifestContent = readManifest(manifestFilePath)
    echo "manifestContent: ${manifestContent}"

    def repoUrl = manifestContent.parameters.parameter.find { it.@name == "repoUrl" }?.@value
    def branchName = manifestContent.parameters.parameter.find { it.@name == "branchName" }?.@value
    def credentials = manifestContent.parameters.parameter.find { it.@name == "Credentials" }?.@value
    echo "RobotframeworkContent: ${repoUrl} ${branchName}${credentials}"

    if (repoUrl && credentials) {
        def robotTestUrl = "${repoUrl}/TestSuits/Example"
        echo "robotTestUrl: ${robotTestUrl}"
        
        // Clone the repo or perform any other necessary actions here.
    } else {
        echo "Incomplete or missing information in the manifest file."
    }
}

def readManifest(String manifestFilePath) {
    def xml = readFile encoding: 'UTF-8', file: manifestFilePath
    def parsedManifest = new XmlSlurper().parseText(xml)
    return parsedManifest
}
