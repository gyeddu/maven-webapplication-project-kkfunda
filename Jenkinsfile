node {
    // Fetch the Maven installation path configured in Jenkins
    def mavenHome = tool name: "maven-3.9.11"

    // Stage 1: Git Checkout
    stage('git checkout') {
        git branch: 'development', credentialsId: 'dc8adbc7-9eca-4b22-80a6-629219bf8e84', url: 'https://github.com/gyeddu/maven-webapplication-project-kkfunda.git'
    }

    // Stage 2: Build the application
    stage('Build') {
        sh "${mavenHome}/bin/mvn clean package"
    }

    // Stage 3: SonarQube Code Quality Report
    stage('SQ Report') {
        sh "${mavenHome}/bin/mvn sonar:sonar"
    }

    // Stage 4: Upload to Nexus Repository
    stage('Deploy into Nexus') {
        sh "${mavenHome}/bin/mvn clean deploy"
    }

    // Stage 5: Deploy WAR to Tomcat using curl
    stage('Deploy to Tomcat') {
        echo "Deploying WAR file using curl..."

        sh """
            curl -u kk:password \
            --upload-file /var/lib/jenkins/workspace/scripted-cicd-job/target/maven-web-application.war \
            "http://54.167.249.228:8080/manager/text/deploy?path=/maven-web-application&update=true"
        """
    }
} // node closing

node {

   echo "git branch name: ${env.JOB_NAME}"
   echo "build number is: ${env.BUILD_NUMBER}"
   echo "node name is: ${env.NODE_NAME}"

    def mavenHome=tool name: "maven3.9.11"

    try {

    stage('git checkout')
    {
      git branch: 'development', credentialsId: 'dc8adbc7-9eca-4b22-80a6-629219bf8e84', url: 'https://github.com/gyeddu/maven-webapplication-project-kkfunda.git'
    }
    stage('compile')
    {
    sh  "${mavenHome}/bin/mvn compile"
    }
    stage('Build')
    {
    sh  "${mavenHome}/bin/mvn clean package"
    }
   stage('SQ Report')
    {
    sh  "${mavenHome}/bin/mvn sonar:sonar"
    }
    stage('Deploy into Nexus')
    {
    sh  "${mavenHome}/bin/mvn clean deploy"
    }
    stage('Deploy to Tomcat') {
        echo "Deploying WAR file using curl..."

        sh """
            curl -u kkfunda:kkfunda \
            --upload-file /var/lib/jenkins/workspace/jio-scripted-way-pipeline-develoment/target/maven-web-application.war \
            "http://54.164.8.72:8080/manager/text/deploy?path=/maven-web-application&update=true"
        """
    }

  } catch (e) {
    // If there was an exception thrown, the build failed
    currentBuild.result = "FAILED"
    throw e
  } finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
}

def notifyBuild(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESS'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESS') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}
