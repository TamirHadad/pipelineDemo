node {
    // git url: 'https://github.com/TamirHadad/pipelineDemo.git'
    def server = rtGetServer('Test')
    def warmUpJson = readFile 'warmup.json'    
    def buildInfo = server.upload(warmUpJson)
    def resolveJson = readFile 'resolve.json' 
    def b2 = server.download(resolveJson)
    buildInfo.append(b2);
    server.publishBuildInfo(buildInfo)
}
