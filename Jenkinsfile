pipeline {
    agent any
    
    tools {
     dockerTool 'Docker'   
    }
    
    stages {
        
        stage('Clone Repo') {
            
            steps{
                
                git branch: 'master', credentialsId: 'Jenkins', url: 'git@github.com:noor-muradi/laravel-calculator.git'
            }
            
        }
        
        stage('SonarQube Analysis'){
            environment {
                def scannerHome = tool 'SonarQubeScanner'
            }
            
            steps{
                withSonarQubeEnv('SonarQube') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
                
            }
            
        }    
        stage('Docker Image Build'){
            steps{
                
                sh 'docker build -t noormuradi/laravel-pipeline .'
                sh 'docker push noormuradi/laravel-pipeline'
            }    
        
        }
        stage('Deploy to remote server'){
            
            steps{
                sh 'ssh user@remote-server docker stop laravel || true && docker rm laravel || true'
                sh 'ssh user@remote-server docker run -it -d --name laravel -p 8181:8181 noormuradi/laravel-pipeline || true'
            }
        }
    }        
            
    post{
        always{
              emailext to: "contact@itsnoor.info", subject: "jenkins build:${currentBuild.currentResult}: ${env.JOB_NAME}", body: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\nMore Info can be found here: ${env.BUILD_URL}"
            
        }
    }
}
