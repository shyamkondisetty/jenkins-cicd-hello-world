pipeline {
    environment {
        DEPLOY = "${env.BRANCH_NAME == "master" || env.BRANCH_NAME == "develop" ? "true" : "false"}"
        NAME = "${env.BRANCH_NAME == "master" ? "example" : "example-staging"}"
        DOMAIN = 'localhost'
        REGISTRY = 'shyamkondisetty/jenkins-CICD-hello-world'
        VERSION = '1.0'
        IMAGE_ID= 'docker.pkg.github.com/shyamkondisetty/jenkins-CICD-hello-world/helloworld'
        REGISTRY_CREDENTIAL = 'githubcredentials'
    }
    agent any
    stages {
        stage('Checkout from github') {
            steps {
                git branch: 'master',
                    credentialsId: '${REGISTRY_CREDENTIAL}',
                    url: 'https://github.com/shyamkondisetty/jenkins-CICD-hello-world.git'
            }
        }
        stage('Build') {
             steps {
                sh './gradlew clean build'
             }
        }
        stage('Docker Build') {
             steps {
                container('docker') {
                    sh "docker build -t image ."
                    sh "docker tag image ${IMAGE_ID}:${VERSION}"
                }
             }
        }
        stage('Docker Publish') {
             steps {
                container('docker') {
                    withDockerRegistry([credentialsId: "${REGISTRY_CREDENTIAL}", url: "git@github.com:shyamkondisetty/jenkins-CICD-hello-world.git"]) {
                        sh "docker push ${IMAGE_ID}:${VERSION}"
                    }
                }
             }
        }
//         stage('Kubernetes Deploy') {
//              steps {
//                 container('helm') {
//                     sh "helm upgrade --install --force --set name=${NAME} --set image.tag=${VERSION} --set domain=${DOMAIN} ${NAME} ./helm"
//                 }
//              }
//         }
    }

}
