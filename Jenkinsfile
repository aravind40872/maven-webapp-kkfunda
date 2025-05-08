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
        script {
          try {
            sh "mvn clean compile"
          } catch (err) {
            slackSend(channel: '#all-devops', color: 'danger', message: "🧹 *Compile stage failed!* Clean code ra babu! 👨‍💻\n${env.BUILD_URL}")
            error("Compile stage failed")
          }
        }
      }
    }

    stage('BUILD') {
      steps {
        script {
          try {
            sh "mvn clean package"
          } catch (err) {
            slackSend(channel: '#all-devops', color: 'danger', message: "📦 *Build stage failed!* Package chakkaga levu ra bro. 🤷‍♂️\n${env.BUILD_URL}")
            error("Build stage failed")
          }
        }
      }
    }

    stage('SQ REPORT') {
      steps {
        script {
          try {
            sh "mvn clean sonar:sonar"
          } catch (err) {
            slackSend(channel: '#all-devops', color: 'danger', message: "🔍 *SonarQube stage failed!* Bugs buggerlu chusi fix chey ra! 🐞\n${env.BUILD_URL}")
            error("SonarQube stage failed")
          }
        }
      }
    }

    stage('Deploy to Nexus') {
      steps {
        script {
          try {
            sh "mvn clean deploy"
          } catch (err) {
            slackSend(channel: '#all-devops', color: 'danger', message: "🚀 *Nexus Deploy failed!* Repo ki reach avvaledu ra! 📦\n${env.BUILD_URL}")
            error("Nexus deploy failed")
          }
        }
      }
    }

    stage('Deploy App') {
      steps {
        script {
          try {
            deploy adapters: [
              tomcat9(
                credentialsId: '3706ed76-0da5-431d-b454-786b6873bd92',
                path: '',
                url: 'http://3.229.139.27:8080/'
              )
            ],
            contextPath: null,
            war: '**/maven-web-application.war'
          } catch (err) {
            slackSend(channel: '#all-devops', color: 'danger', message: "🌐 *Tomcat Deploy failed!* Server daggara bottu padindi! 🔥\n${env.BUILD_URL}")
            error("Tomcat deploy failed")
          }
        }
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
        message: "❌ Pipeline '${env.JOB_NAME} #${env.BUILD_NUMBER}' failed somewhere. Go check and fix it, Captain! 👨‍🔧\n${env.BUILD_URL}"
      )
    }
  }

} // pipeline
