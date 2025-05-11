pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/ankushregmi01/ShoppingCart-CI-CD.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile -X'
            }
        }

        stage('Sonarqube Analysis') {
            steps {
                sh ''' 
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.host.url=http://localhost:9000/ \
                    -Dsonar.login=squ_5537cf0bd230c89cedb5d9a670fea0a76b16ad95 \
                    -Dsonar.projectName=secret-santa \
                    -Dsonar.projectKey=secret-santa \
                    -Dsonar.java.binaries=.
                '''
            }
        }
        stage('Build JAR') {
            steps {
                sh 'mvn clean package -Dmaven.test.failure.ignore=true'
                sh 'ls -la target/'
            }
        }
        
        stage('Docker Build and Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', url: 'https://index.docker.io/v1/') {
                        sh "docker build -t santa ."
                        sh "docker tag santa ankushregmigg/santa:latest"
                        sh "docker push ankushregmigg/santa:latest"
                    }
                }
            }
        }
        
        stage('Docker Deploy to Ccointainer') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', url: 'https://index.docker.io/v1/') {
                        sh "docker run -d --name santa-build -p 8087:8087 ankushregmigg/santa:latest"
                    }
                }
            }
        }
    }
}
