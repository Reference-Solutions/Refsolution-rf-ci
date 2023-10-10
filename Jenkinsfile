//  Read manifest file 
// - Clone test-html-report repo 
// - Execute robot test 

script.stage("Read manifest file") {
    runTask(env, stageInput)
} 
script.stage("Execute RF") {
    executeRobotFrameworkTests(env, stageInput)
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
       
    // Check the value of the pip.runTask environment variable
    // def runTask = env.'pip.runTask'

    //     if (runTask == 'true') {
            if (repoUrl && credentials) {
                // Construct the full URL to the Robot Framework test
                def robotTestUrl = "${repoUrl}/TestSuits/Example"
                logger.info("robotTestUrl: ${robotTestUrl}")
            } else {
                println("Incomplete or missing information in the manifest file.")
            }
        } 
        // else {
        //     println("Make the method as true")
        // }


    // if (repoUrl && credentials) {
    //     // Construct the full URL to the Robot Framework test
    //     def robotTestUrl = "${repoUrl}/TestSuits/Example"
    //     logger.info("robotTestUrl: ${robotTestUrl}")
    // } else {
    //     println("Incomplete or missing information in the manifest file.")
    // }

void executeRobotFrameworkTests(Map env, Map stageInput) {
    // Construct the Robot Framework command
    def includeTags = "regression example"
    def excludeTags = "sanity example"
       

    def robot_options = "--outputdir reports " +
                        "--variable BROWSER:chrome " +
                        "--loglevel DEBUG " +
                        "--consolecolors on"

    def robot_test_dir = env.ROBOT_TEST_DIR  // Update with your test directory

    script.bat """
        echo 'RF execution starts'
        python -m robot.run ${robot_options} ${robot_test_dir}
    """
        // Publish HTML reports
        // python -m robot.run -d ./Results/Lastrun --pythonpath Test-HTML-Report/TestKetwords/Example/ --consolecolors on ${robot_test_dir}
    publishHTML()
}

void publishHTML() {
    // Publish HTML reports (modify this as needed)
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

def readManifest(String manifestFilePath) {
    def xml = script.readFile(encoding: 'UTF-8', file: manifestFilePath)
    def parsedManifest = new groovy.util.XmlParser().parseText(xml)
    parsedManifest
 }