pipeline {
    agent any
    tools {
        maven 'maven3.9.9'
    }
    stages {
//         stage('Checkout SCM') {
//             steps {
//                 git branch: 'master', url: 'https://github.com/DevRodrigoSV/spring-petclinic-rest.git'
//             }
//         }
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
                success {
                    junit 'target/surefire-reports/*.xml'
                }
                failure {
                    echo 'Tests failed!'
                }
            }
        }
        stage('Coverge') {
            steps {
                sh 'mvn jacoco:report -B -ntp'
            }
            post {
                success {
                    //recordCoverage(tools: [[parser: 'JACOCO']])
                    recordJacoco(
                        tools: [[parser: 'JACOCO']],
                        sourceCodeRetention: 'EVERY_BUILD',
                        qualityGates: [
                            [threshold: 30.0, metric: 'LINE'],
                            [threshold: 30.0, metric: 'BRANCH']
                        ]
                    )
                }
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
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            cleanWs()
        }
    }
}
