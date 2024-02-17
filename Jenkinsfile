pipeline {
    agent any
    environment {
        SCANNER_HOME= tool'sonar-scanner'
    }
    stages {
        stage("gitcheckout") {
            steps {
                git branch: 'main', url: 'https://github.com/mohit-decoder/Valentine_day-Project.git'
            }
        } 
        stage("trivy-filesystem scans") {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }   
        stage("Sonarqube analysis") {
            steps {
                withSonarQubeEnv('sonar') {
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Valentine -Dsonar.projectName=Valentine"
                }
            }
        }     
        stage("Build & tag docker images") {
            steps {
                withDockerRegistry(credentialsId: 'docker-cred', url: 'https://index.docker.io/v1/') {
                    sh "docker build -t mohitdecoder/valentine:v1 ."
                }
            }
        }   
        stage("Trivy Image scan") {
            steps {
                sh "trivy image --format json -o trivy-image-report.json mohitdecoder/valentine:v1"
            }
        }   
        stage("Push docker image") {
            steps {
                withDockerRegistry(credentialsId: 'docker-cred', url: 'https://index.docker.io/v1/') {
                    sh "docker push mohitdecoder/valentine:v1 "
                }
            }
        } 
        stage("Deploy-to-Container") {
            steps {
                sh "docker run -d -p 8082:80 mohitdecoder/valentine:v1"
            }
        }     
    }
}
