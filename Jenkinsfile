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
                              --publish 5672:5672 \
                              --publish 15672:15672 \
                              --env RABBITMQ_DEFAULT_USER=guest \
                              --env RABBITMQ_DEFAULT_PASS=guest \
                              rabbitmq'

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