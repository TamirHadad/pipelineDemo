node {
    // git url: 'https://github.com/TamirHadad/pipelineDemo.git'
    
    // get server from Artifactory global variable
    def server = Artifactory.server('Test')
    def warmUpJson = readFile 'warmup.json'    
    withEnv(['AAAA=BBBB']) {
        // the buildInfo will contains the environment variables in this scope
        buildInfo = server.upload(warmUpJson)
       
  }
    def buildInfo = server.upload(warmUpJson)
    def resolveJson = readFile 'resolve.json' 
    def b2 = server.download(resolveJson)
    // merge the buildInfo data
    buildInfo.append(b2);
    // publish buildInfo to artifactory
    server.publishBuildInfo(buildInfo)
}
