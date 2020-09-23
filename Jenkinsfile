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
            checkout scm
          }
        }

        stage('Run xyz unit tests') {
          steps {
            echo "Running unit test cases on creation of pull request."
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
             sh "export AWS_PROFILE=staging"
             sh "ls -l target/javaparser-maven-sample-?.?-SNAPSHOT.jar"
             sh "aws s3 cp target/javaparser-maven-sample-?.?-SNAPSHOT.jar  s3://locus-bucket/javaparser-maven-sample.jar"
          }
        }
    }
}