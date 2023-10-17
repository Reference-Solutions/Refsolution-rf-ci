//def runTask(Map env, currentBuild) {
//void runTask(Map env, Map stageInput) {
def runTask(stageInput) {
    script {
    echo "TODO: Verification"
    //def manifestFilePath = currentBuild.getEnvVars()["MANIFEST_FILE_PATH"]
    String manifestFilePath = stageInput.manifest_file_path

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
}

def readManifest(String manifestFilePath) {
    //def xml = readFile encoding: 'UTF-8', file: manifestFilePath
    //def parsedManifest = new XmlSlurper().parseText(xml)
    def xml = script.readFile(encoding: 'UTF-8', file: manifestFilePath)
    def parsedManifest = new groovy.util.XmlParser().parseText(xml)
    parsedManifest
    // return parsedManifest
}

pipeline {
    agent { label 'windows-lab-pc' }

    stages {
        stage('Read manifest file') {
            steps {
                script {
                    bat "echo Hello"
                    def stageInput = [
                        manifest_file_path: 'refsolution-rf-ci/manifest.xml'

                    ]
                    //runTask(env, currentBuild.rawBuild)
                    //runTask(env, stageInput)
                    runTask(stageInput)

                }
            }
        }
    }
}

