pipeline {
    agent any
    tools {
        maven 'maven3.8.5'
    }
    stages {

        stage('Compile') {
            steps {
                sh 'mvn clean compile -B -ntp'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn test -B -ntp'
            }
        }
        stage('Package') {
            steps {
                sh 'mvn package -DskipTests -B -ntp'
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
