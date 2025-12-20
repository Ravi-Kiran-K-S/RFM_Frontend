pipeline {
    agent any
    environment {
        DOCKER_REGISTRY = credentials('docker-registry-url')
        DOCKER_CREDENTIALS = credentials('dockerlogin')
        ANSIBLE_INVENTORY = credentials('ansible-inventory-path')
    }
    stages {
        stage('Publish frontend') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerlogin', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker build -t ${DOCKER_USER}/rfm_frontend:${BUILD_NUMBER} .
                    docker tag ${DOCKER_USER}/rfm_frontend:${BUILD_NUMBER} ${DOCKER_USER}/rfm_frontend:latest
                    docker push ${DOCKER_USER}/rfm_frontend:${BUILD_NUMBER}
                    docker push ${DOCKER_USER}/rfm_frontend:latest
                    '''
                    }
                }
            }
        }
    }
    post {
        success {
            echo "✓ Pipeline completed successfully"
            sh 'ansible-playbook -i ${ANSIBLE_INVENTORY} deploy-frontend.yml'
            sh 'docker logout || true'
            cleanWs()
        }
        failure {
            echo "✗ Pipeline failed. Check logs for details."
        }
    }
}