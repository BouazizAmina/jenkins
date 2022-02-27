pipeline {
  agent any
  stages {
    stage('build') {
      post {
        failure {
          script {
            mail="Build failed"
          }

        }

        success {
          script {
            mail="Build succeeded"
          }

        }

      }
      steps {
        bat 'gradle build'
        bat 'gradle javadoc'
        archiveArtifacts 'build/libs/*.jar'
        junit(testResults: 'build/test-results/test/*.xml', allowEmptyResults: true)
      }
    }

    stage('Mail Notification') {
      steps {
        mail(subject: 'Jenkins notification', body: mail, cc: 'ia_kermiche@esi.dz', bcc: 'ic_rouzzi@esi.dz')
      }
    }

    stage('Code Analysis') {
      parallel {
        stage('Code Analysis') {
          steps {
            withSonarQubeEnv('My SonarQube Server') {
              bat(script: 'gradle sonarqube', returnStatus: true)
            }

            waitForQualityGate true
          }
        }

        stage('Test Reporting') {
          steps {
            cucumber 'reports/*json'
          }
        }

      }
    }

    stage('Deployment') {
      steps {
        bat 'gradle publish'
      }
    }

    stage('Slack Notification') {
      steps {
        slackSend(token: 'T02SE6G6BC2/B02SSSMPFRP/W46DvXVHWtU09v3O5QaM3ts4', baseUrl: 'https://hooks.slack.com/services/', channel: '#ogl', message: 'The slack notification is sent successfully')
      }
    }

  }
  environment {
    mail = ''
  }
}