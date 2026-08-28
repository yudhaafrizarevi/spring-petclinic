pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'Maven-3.9'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                sh 'mvn clean verify'
            }
        }

        stage('SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                        mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
                        -Dsonar.projectKey=spring-petclinic
                    '''
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t spring-petclinic:latest .'
            }
        }

        stage('Docker Run') {
            steps {
                sh '''
                    docker stop spring-petclinic || true
                    docker rm spring-petclinic || true
                    docker run -d \
                        --name spring-petclinic \
                        -p 8081:8080 \
                        spring-petclinic:latest
                '''
            }
        }
    }
}
