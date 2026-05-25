pipeline {
    agent {
        label 'slave-node'
    }

    tools {
        maven 'Maven'
        jdk 'JDK21'
    }

    stages {

        stage('Check Java Version') {
            steps {
                sh 'java -version'
            }
        }

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                url: 'https://github.com/spring-projects/spring-petclinic.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy to QA') {
            steps {
                sshagent(['00979b3e-3dd9-4883-8bc1-98c4e0352514']) {

                    sh '''
                    scp target/*.jar ubuntu@172.31.45.236:/home/ubuntu/

                    ssh ubuntu@172.31.45.236 "
                    pkill java || true
                    nohup java -jar /home/ubuntu/*.jar > app.log 2>&1 &
                    "
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline Success'
        }

        failure {
            echo 'Pipeline Failed'
        }

        always {
            junit '**/target/surefire-reports/*.xml'
        }
    }
}
