pipeline{

    agent any

    stages{

        stage('Git Checkout'){

            steps{
                git branch: 'main', url: 'https://github.com/Anusha2710/demo-counter-app.git'
            }
        }
        stage('UNIT Testing'){

            steps{
                sh 'mvn test'
            }
        }
        stage('Integration Testing'){

            steps{
                sh 'mvn verify -DskipUnitTests'
            }
        }
        stage('Maven Build'){

            steps{
                sh 'mvn clean install'
            }    
        }
        stage('Static Code Analysis'){

            steps{
                withSonarQubeEnv(credentialsId: 'sonar-api-key')
                sh 'mvn clean package sonar:sonar'
            }    
        }
    }
}