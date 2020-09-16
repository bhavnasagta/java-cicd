properties([pipelineTriggers([githubPush()])])

pipeline {
    agent any
    stages {
       stage('Clean WorkSpace') {
        steps {
           sh 'rm -rf *'        
        }
       }

        stage('Checkout SCM') {
            steps {
                script {
                      if  (env.BRANCH_NAME != 'master') {
                       echo '*************************************************'
                       echo 'There is no Closed Pull Request in Master branch'
                       echo '*************************************************'
                       sh 'exit 1'
                      }
                      else
                      checkout scm
                 }
                }
            }
        }

        stage('Build') {
            when {
                branch 'master'
              }
            steps {
               sh 'mvn -B -Dresume=false release:perform -Darguments="-DskipTests"'
               sh 'mvn clean install -DskipTests=true'
            }
        }

        stage('Pushing latest jar to S3') {
          steps {
             sh "ls -l target/javaparser-maven-sample-?.?-SNAPSHOT.jar"
             sh "aws s3 cp target/javaparser-maven-sample-?.?-SNAPSHOT.jar  s3://locus-bucket/javaparser-maven-sample.jar"
          }
        }
    }
        post {
            success {
              sh 'curl "https://api.GitHub.com/repos/bhavnasagta/java-cicd/statuses/$GIT_COMMIT?access_token=344702019fa7c59d63907cd8241c518d189324d8" \
          -H "Content-Type: application/json" \
          -X POST \
          -d "{\"state\": \"success\",\"context\": \"continuous-integration/jenkins\", \"description\": \"Jenkins\", \"target_url\": \"ci.elanic.co/job/java-cicd-pipeline/$BUILD_NUMBER/console\"}"'
            }
            failure {
              sh 'curl "https://api.GitHub.com/repos/bhavnasagta/java-cicd/statuses/$GIT_COMMIT?access_token=344702019fa7c59d63907cd8241c518d189324d8" \
          -H "Content-Type: application/json" \
          -X POST \
          -d "{\"state\": \"failure\",\"context\": \"continuous-integration/jenkins\", \"description\": \"Jenkins\", \"target_url\": \"ci.elanic.co/job/java-cicd-pipeline/$BUILD_NUMBER/console\"}"'
            }
        }
}