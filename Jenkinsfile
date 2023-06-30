pipeline {
    agent any

    environment {
        function_name = 'peru'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building'
                sh 'mvn package'
            }
        }

       stage("build & SonarQube analysis") {
            agent any
            steps {
              withSonarQubeEnv('Sonar') {
                sh 'mvn sonar:sonar'
              }
            }
          }

        stage('Push') {
            steps {
                echo 'Push'

                sh "aws s3 cp target/sample-1.0.3.jar s3://manchiperu"
            }
        }

        stage('Deploy') {
            steps {
                echo 'Build'

                sh "aws lambda update-function-code --function-name $function_name --region us-east-2 --s3-bucket manchiperu --s3-key sample-1.0.3.jar"
            }
        }
    }
}
