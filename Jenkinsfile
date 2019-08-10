node () {
  def container
 // def  dockerTool = tool name: 'Docker', type: 'org.jenkinsci.plugins.docker.commons.tools.DockerTool'
    environment {
        // This can be nexus3 or nexus2
        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        // Where your Nexus is running
        NEXUS_URL = "http://localhost:8081"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "test"
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "NexusID"
    }
    
    stage('Initialize'){
        def dockerHome = tool 'Docker'
        def mavenHome  = tool 'Maven 3.6.1'
        env.PATH = "${dockerHome}:${mavenHome}/bin:${env.PATH}"
    }

    stage('Clone repository') {
                checkout scm
       shortCommit = bat(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
    }
 

    stage('Build image') {
     container = docker.build('visibilityspots/jenkins-docker')
    // sh 'docker build -t "visibilityspots/jenkins-docker" .'
       }
  

    stage('Test image') {
      bat 'export GOSS_FILES_STRATEGY=cp && /usr/local/bin/dgoss  run --name jenkins-docker-dgoss-test --rm -ti visibilityspots/jenkins-docker'
    }

    stage('Push image') {
      docker.withRegistry('$NEXUS_URL', '$NEXUS_CREDENTIAL_ID') {
        container.push("${shortCommit}")
        container.push('latest')
      }
    }
}
