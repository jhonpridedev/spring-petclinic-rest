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
        stage('Package') {
            steps {
                sh 'mvn package -DskipTests -B -ntp'
            }
        }
        // stage('Sonarqube') {
        //     steps {
        //         withSonarQubeEnv('sonarqube'){
        //             // si ya se tiene configurado el sonar en jenkins como plugin
        //             sh 'mvn sonar:sonar -B -ntp'
        //         }                
        //     }
        // }
        // stage('Quality Gate') {
        //     // espere 1 hora para la respuesate del sonar sobre codigo scaneado
        //     steps {
        //         timeout(time: 1, unit: 'HOURS'){
        //             waitForQualityGate abortPipeline: true
        //         }
        //     }
        // }



        // stage('Artifactory') {
        //     steps {
        //         script {
        //             // Forma 1 - Artifactory, viene del plugin instalado
        //             sh 'printenv'
        //             // env.MAVEN_HOME = '/usr/share/maven'

        //             def releases = 'spring-petclinic-rest-release'
        //             def snapshots = 'spring-petclinic-rest-snapshop'
        //             def server = Artifactory.server 'artifactory'
        //             def rtMaven = Artifactory.newMavenBuild()
        //             rtMaven.deployer server: server, releaseRepo: releases, snapshotRepo: snapshots
        //             def buildInfo = rtMaven.run pom: 'pom.xml', goals: 'clean package -B -ntp -DskipTests'
        //             server.publishBuildInfo buildInfo
        //         }
        //     }
        // }

        stage('Artifactory') {
            steps {
                script {
                     // Forma 2 - File Spec

                    sh 'env | sort'

                    def pom = readMavenPom file: 'pom.xml'
                    println pom

                    def server = Artifactory.server 'artifactory'
                    def repository = 'spring-petclinic-rest'

                    if("${GIT_BRANCH}" == 'origin/master'){
                        repository = repository + '-release'
                    } else {
                        repository = repository + '-snapshot'
                    }

                    def uploadSpec = """
                        {
                            "files": [
                                {
                                    "pattern": "target/.*.jar",
                                    "target": "${repository}",
                                    "regexp": "true"
                                }
                            ]
                        }
                    """
                    server.upload spec: uploadSpec
                }
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
