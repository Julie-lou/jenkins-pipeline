pipeline{
    agent any
    parameters {
        string(
            name: "BRANCH_NAME",
            defaultValue: '',
            description: 'Git branch to build'
        )
    }

    environment{
        DOCKER_IMAGE= 'julielou/php-ci-app'
    }
    stages{
        stage('Checkout'){
            steps{
                git branch: "${params.BRANCH_NAME}",
                    url: "https://github.com/Julie-lou/php-ci-app.git"
            }
        }
        stage('Build & Push'){
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            -u "$DOCKER_USERNAME" \
                            --password-stdin
                        
                        docker build \
                        -t ${DOCKER_IMAGE}:${BUILD_NUMBER} \
                        -t ${DOCKER_IMAGE}:lates \
                        .

                        docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
                        docker push ${DOCKER_IMAGE}:latest

                        '''
                }
            }
        }
        stage('Clean local Image'){
            steps{
                sh '''
                    docker rmi ${DOCKER_IMAGE}:${BUILD_NUMBER} || true
                    docker rmi ${DOCKER_IMAGE}:lates || true
                    '''
                     
            }
        }
    }
    post{
        success{
            echo 'CI pipeline completed successfully!'
        }
        failure{
            echo 'CI pipeline failed!'
        }
        always{
            sh 'docker logout || true'
        }
    }
}