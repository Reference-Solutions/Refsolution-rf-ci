//  Read manifest file 
// - Clone test-html-report repo 
// - Execute robot test 

script.stage("Read manifest file") {
    runTask(env, stageInput)
} 

void runTask(Map env, Map stageInput) {
    logger.info("TODO: Verification")
    String manifestFilePath = stageInput.manifest_file_path
    logger.info("manifestFilePath: ${manifestFilePath}")
    
    def manifestContent = readManifest(manifestFilePath) // Read entire manifest content
    logger.info("manifestContent: ${manifestContent}")

    // Extract URL and credentials from the manifest
    def repoUrl = manifestContent.parameters.parameter.find { it.@name == "repoUrl" }?.@value
    def branchName = manifestContent.parameters.parameter.find { it.@name == "branchName" }?.@value
    def credentials = manifestContent.parameters.parameter.find { it.@name == "Credentials" }?.@value
    logger.info("RobotframeworkContent: ${repoUrl} ${branchName}${credentials}")
    
            if (repoUrl && credentials) {
                // Construct the full URL to the Robot Framework test
                def robotTestUrl = "${repoUrl}/TestSuits/Example"
                logger.info("robotTestUrl: ${robotTestUrl}")
            } else {
                println("Incomplete or missing information in the manifest file.")
            }
        } 

def readManifest(String manifestFilePath) {
    def xml = script.readFile(encoding: 'UTF-8', file: manifestFilePath)
    def parsedManifest = new groovy.util.XmlParser().parseText(xml)
    parsedManifest
 }