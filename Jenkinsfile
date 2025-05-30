pipeline {
    agent any
    tools {
        maven 'maven3.9.9'
    }
    stages {
        stage('Checkout SCM') {
            steps {
                git branch: 'master', url: 'https://github.com/DevRodrigoSV/spring-petclinic-rest.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package -B -ntp'
            }
        }
    }
}
