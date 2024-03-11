pipeline {
    agent any
    tools {
        maven 'maven3.8.5'
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/jhonpridedev/spring-petclinic-rest'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests -B -ntp'
            }
        }
    }
    post {
        success {
            archiveArtifacts artifacts: 'target/*.jar', followSymlinks: false
            deleteDir()
        }
    }
}
