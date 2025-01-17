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
                script{
                        withSonarQubeEnv(credentialsId: 'sonar-api-key'){
                            sh 'mvn clean package sonar:sonar'
                        }       
                }  
            }    
        }
        stage('Quality Gate Status'){
            steps{
                script{
                        waitForQualityGate abortPipeline: false, credentialsId: 'sonar-api-key'
                        }       
                }  
            } 
            stage('Upload WAR file to Nexus'){
            steps{
                script{

                        def readPomVersion = readMavenPom file: 'pom.xml'

                        def nexusRepo = readPomVersion.version.endsWith("SNAPSHOT") ? "demoapp-snapshot" : "demoapp-release"
                       
                        nexusArtifactUploader artifacts: 
                        [
                            [
                                artifactId: 'springboot',
                                classifier: '', file: 'target/Uber.jar',
                                type: 'jar'
                                ]
                    
                            ],
                            credentialsId: 'nexus-auth',
                            groupId: 'com.example',
                            nexusUrl: '3.11.68.104:8081', 
                            nexusVersion: 'nexus3',
                            protocol: 'http',
                            repository: nexusRepo,
                            version: "${readPomVersion.version}"
                     }       
                }  
            }
            stage('Docker Image Build'){

                steps{

                    script{
                        sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
                        sh 'docker image tag $JOB_NAME:v1.$BUILD_ID anushamaddela1997/$JOB_NAME:v1.$BUILD_ID'
                        sh 'docker image tag $JOB_NAME:v1.$BUILD_ID anushamaddela1997/$JOB_NAME:latest'
                    }
                }
            }
            stage('Push image to Dockerhub'){

                steps{

                    script{

                        withCredentials([string(credentialsId: 'docker-auth', variable: 'docker-hub-cred')]) {
                            
                            sh 'docker login -u anushamaddela1997 -p ${docker-hub-cred}'
                            sh 'docker image push anushamaddela1997/$JOB_NAME:v1.$BUILD_ID'
                            sh 'docker image push anushamaddela1997/$JOB_NAME:latest'

    
                    }
                    }
                }
            }       
        }
}