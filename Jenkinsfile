pipeline {
  agent any

  tools {
    maven "maven"
  }

  triggers {
    pollSCM('* * * * *')
  }

  stages {

    stage('Notify Start') {
      steps {
        slackSend(
          channel: '#all-devops',
          color: '#439FE0',
          message: "🟡 STARTED: Pipeline '${env.JOB_NAME} #${env.BUILD_NUMBER}' has started.\nCheck progress: ${env.BUILD_URL}"
        )
      }
    }

    stage('checkout') {
      steps {
        git branch: 'dev',
            credentialsId: '886f47e6-7978-490e-aeee-3b4bab000dea',
            url: 'https://github.com/aravind40872/maven-webapp-kkfunda.git'
      }
    }

    stage('COMPILE') {
      steps {
        sh "mvn clean compile"
      }
    }

    stage('BUILD') {
      steps {
        sh "mvn clean package"
      }
    }

    stage('SQ REPORT') {
      steps {
        sh "mvn clean sonar:sonar"
      }
    }

    stage('Deploy to Nexus') {
      steps {
        sh "mvn clean deploy"
      }
    }

    stage('Deploy App') {
      steps {
        deploy adapters: [
          tomcat9(
            credentialsId: '3706ed76-0da5-431d-b454-786b6873bd92',
            path: '',
            url: 'http://3.229.139.27:8080/'
          )
        ],
        contextPath: null,
        war: '**/maven-web-application.war'
      }
    }

    stage('Slack Notification') {
      steps {
        slackSend(
          channel: '#all-devops',
          color: 'good',
          message: "✅ SUCCESS: Pipeline '${env.JOB_NAME} #${env.BUILD_NUMBER}' completed successfully.\nView results: ${env.BUILD_URL}"
        )
      }
    }

  } // stages

  post {
    failure {
      slackSend(
        channel: '#all-devops',
        color: 'danger',
        message: "❌ FAILURE: Pipeline '${env.JOB_NAME} #${env.BUILD_NUMBER}' failed.\nDetails: ${env.BUILD_URL}"
      )
    }
  }

} // pipeline
