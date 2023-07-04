pipeline {
    agent any

    environment {
        function_name = 'peru'
    }

    stages {
        stage('Buildd') {
            steps {
                echo 'Building'
                sh 'mvn package'
            }
        }

       stage("build & SonarQube analysis") {
            agent any
            when {
                anyOf {
                    branch 'feature/*'
                    branch 'main'
                }
            }

            steps {
              withSonarQubeEnv('Sonar') {
                sh 'mvn sonar:sonar'
              }
            }
          }

         stage("Quality Gate") {
            steps {
                script {
                try {
              timeout(time: 10, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
              }
              }
              catch (Exception ex) {


              }
                }
    
            }
          }

        stage('Push') {
            steps {
                echo 'Push'

                sh "aws s3 cp target/sample-1.0.3.jar s3://manchiperu"
            }
        }

        // cd start

        stage('Deployments') {
            parallel {

        stage('Deploy to dev') {
            steps {
                echo 'Build'

                sh "aws lambda update-function-code --function-name $function_name --region us-east-2 --s3-bucket manchiperu --s3-key sample-1.0.3.jar"
            }
        }

        stage('Deploy to test') {
            when {
                branch 'main'
            }
            steps {
                echo 'Build'

                sh "aws lambda update-function-code --function-name $function_name --region us-east-2 --s3-bucket manchiperu --s3-key sample-1.0.3.jar"
            }
        }
            }

        }

       
    }
}
