pipeline {
  agent any
  stages {
    stage('Test') {
      when {
        environment name: 'run_test_only', value: 'yes'
      }
      steps {
        sh 'cd examples/java-calculator && mvn clean integration-test'
      }
    }
    stage('Run demo parallel stages') {
      parallel {
        stage('Parallel stage #1') {
          steps {
            script {
              if (env.run_test_only =='yes')
              {
                echo env.firstEnvVar
              }
              else
              {
                echo env.secondEnvVar
              }
            }

          }
        }
        stage('Parallel stage #2') {
          steps {
            echo "${thirdEnvVar}"
          }
        }
      }
    }
  }
  environment {
    firstEnvVar = 'FIRST_VAR'
    secondEnvVar = 'SECOND_VAR'
    thirdEnvVar = 'THIRD_VAR'
  }
  post {
    success {
      echo 'Test succeeded'
      script {
        mail(bcc: '',
        body: "Run ${JOB_NAME}-#${BUILD_NUMBER} succeeded. To get more details, visit the build results page: ${BUILD_URL}.",
        cc: '',
        from: 'jenkins-admin@gmail.com',
        replyTo: '',
        subject: "${JOB_NAME} ${BUILD_NUMBER} succeeded",
        to: env.notification_email)
        if (env.archive_war =='yes')
        {
          // ArchiveArtifact plugin
          archiveArtifacts '**/java-calculator-*-SNAPSHOT.jar'
        }
        // Cucumber report plugin
        cucumber fileIncludePattern: '**/java-calculator/target/cucumber-report.json', sortingMethod: 'ALPHABETICAL'
      }


    }

    failure {
      echo 'Test failed'
      mail(bcc: '', body: "Run ${JOB_NAME}-#${BUILD_NUMBER} succeeded. To get more details, visit the build results page: ${BUILD_URL}.", cc: '', from: 'marciojardel@gmail.com', replyTo: '', subject: "${JOB_NAME} ${BUILD_NUMBER} failed", to: env.notification_email)
      cucumber(fileIncludePattern: '**/java-calculator/target/cucumber-report.json', sortingMethod: 'ALPHABETICAL')

    }

  }
  parameters {
    choice(choices: '''yes
no''', description: 'Are you sure you want to execute this test?', name: 'run_test_only')
    choice(choices: '''yes
no''', description: 'Archived war?', name: 'archive_war')
    string(defaultValue: 'your.email@gmail.com', description: 'email for notifications', name: 'notification_email')
  }
}