#!groovy

pipeline {
    agent any
    options {
        timestamps()
    }
    environment {
        MAJOR_VERSION = "1"

        DOCKER_REGISTRY_CREDENTIALS_ID = "docker_credentials_hub"

        IMAGE_TAG = "youscan/logspout"
        VERSION = "${env.MAJOR_VERSION + '.' + env.BUILD_NUMBER + (env.BRANCH_NAME == 'master' ? '' : '-' + env.BRANCH_NAME)}"
    }
    stages {
        stage('Build') {
            agent {
                docker { image 'docker' }
            }
            steps {
                script {
                    currentBuild.displayName = "${env.VERSION}"
                    withCredentials([usernamePassword(credentialsId: env.DOCKER_REGISTRY_CREDENTIALS_ID, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]){
                        sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
                        sh "docker build -t $IMAGE_TAG:$VERSION custom"
                        sh "docker tag $IMAGE_TAG:$VERSION $IMAGE_TAG"
                        sh "docker push $IMAGE_TAG:$VERSION"
                        sh "docker push $IMAGE_TAG"
                        sh "docker rmi -f $IMAGE_TAG:$VERSION"
                        sh "docker rmi -f $IMAGE_TAG"
                    }
                }
                sh 'node --version'
            }
        }
    }
}