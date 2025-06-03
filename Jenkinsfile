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
    triggers {
        //cron('* * * * *')
        githubPush()
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
        stage('SonarQube') {
            steps {
                withSonarQubeEnv('sonarqube'){
                    sh 'env | sort'
                    script {
                        if (env.CHANGE_ID) {
                            sh """
                                mvn sonar:sonar -B -ntp \
                                -Dsonar.pullrequest.key=${env.CHANGE_ID} \
                                -Dsonar.pullrequest.branch=${env.CHANGE_BRANCH} \
                                -Dsonar.pullrequest.base=${env.CHANGE_TARGET}
                            """
                        } else {
                            def branchName = GIT_BRANCH.replaceFirst('^origin/', '')
                            println "Branch name: ${branchName}"
                            sh "mvn sonar:sonar -B -ntp -Dsonar.branch.name=${branchName}"
                        }
                    }
                }
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
