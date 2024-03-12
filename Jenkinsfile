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
        stage('Test') {
            steps {
                sh 'mvn test -B -ntp'
            }
            post {
                // si el step se ejecuta bien, este post tambien se va a ejecutar bien
                success {
                    jacoco()  // parla cobertura de preubas unitarias
                    junit 'target/surefire-reports/*.xml'
                }
                always {
                    echo 'siempre se ejecuta'
                }
                failure {
                    echo 'si falla los test'
                }
            }
        }
        stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonarqube')
                // si ya se tiene configurado el sonar en jenkins como plugin
                sh 'mvn sonar:sonar -B -ntp'
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
