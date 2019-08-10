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
      DOCKER_CERT_PATH='C:\Users\Keerti\.docker\machine\machines\default'
DOCKER_HOST=tcp://192.168.99.100:2376
DOCKER_MACHINE_NAME=default
DOCKER_TLS_VERIFY=1
DOCKER_TOOLBOX_INSTALL_PATH='C:\Program Files\Docker Toolbox'
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
     container = sudo docker.build('visibilityspots/jenkins-docker')
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
