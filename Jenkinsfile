node {
    def server = SERVER_ID: artifactory
    def rtMaven = Artifactory.newMavenBuild()
    def buildInfo

    stage ('Clone') {
        git url: 'https://github.com/JFrog/project-examples.git'
    }

    stage ('Artifactory configuration') {
        rtMaven.tool = 'M3' // Tool name from Jenkins configuration
        rtMaven.deployer releaseRepo: 'libs-release-local', snapshotRepo:'libs-snapshot-local', server: server
        rtMaven.resolver releaseRepo: 'libs-release', snapshotRepo:'libs-snapshot', server: server
        buildInfo = Artifactory.newBuildInfo()
    }

    stage ('Exec Maven') {
        docker.image('maven:3.6.1-jdk-8-slim').inside {
            withEnv(['JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_241.jdk/Contents/Home']) { // Java home of the container
                rtMaven.run pom: 'maven-example/pom.xml', goals: 'clean install', buildInfo: buildInfo
            }
        }
    }

    stage ('Publish build info') {
        server.publishBuildInfo buildInfo
    }
}
