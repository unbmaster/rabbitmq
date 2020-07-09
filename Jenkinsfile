pipeline {

    agent any

    options {
        skipDefaultCheckout(true)
    }
    stages {

        stage('Checkout GitHub') {
            steps{
                cleanWs()
                script {
                    try {
                        git([url: 'https://github.com/unbmaster/rabbitmq', branch: 'master'])
                    } catch (Exception e) {
                    sh "echo $e; exit 1"
                    }
                }
            }
        }

        stage('Deploy RabbitMQ') {
            steps{
                script {
                    sh 'docker service rm rabbitmq || true'
                    try {
                        sh 'docker service create \
                              --name rabbitmq \
                              --mode replicated \
                              --replicas 1 \
                              --network app-net \
                              --endpoint-mode dnsrr \
                              --env RABBITMQ_DEFAULT_USER=guest \
                              --env RABBITMQ_DEFAULT_PASS=guest \
                              --mount type=bind,src=/var/lib/jenkins/workspace/${JOB_NAME},dst=/var/lib/rabbitmq,ro=true \
                              rabbitmq:3-management'

                    } catch (Exception e) {
                        sh "echo $e; exit 1"
                        currentBuild.result = 'ABORTED'
                        error('Erro')
                    }
                }
            }
        }

    }
}