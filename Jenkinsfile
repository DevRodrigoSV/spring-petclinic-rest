pipeline {
    //agent any
    agent {
        docker {
            image 'maven:3.9.9-eclipse-temurin-17-alpine'
        }
    }
    //tools {
    //    maven 'maven3.9.9'
    //}
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
                    // recordCoverage(tools: [[parser: 'JACOCO']])
                    discoverReferenceBuild()
                    recordCoverage(
                        tools: [[parser: 'JACOCO']],
                        sourceCodeRetention: 'EVERY_BUILD',
                        qualityGates: [
                            [threshold: 80.0, metric: 'LINE', criticality: 'FAILURE'],
                            [threshold: 60.0, metric: 'BRANCH', criticality: 'FAILURE']
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
