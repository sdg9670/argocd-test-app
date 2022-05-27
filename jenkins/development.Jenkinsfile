pipeline {
    agent any

    stages {
        stage('checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/sdg9670/argocd-test-app.git'
            }
        }

        // stage('docker build') {
        //     steps {
        //         sh 'docker build -t docker.registry.com/argocd-test-app-dev:${BUILD_NUMBER} .'
        //     }
        // }

        // stage('docker push') {
        //     steps {
        //         sh 'docker push docker.registry.com/argocd-test-app-dev:${BUILD_NUMBER}'
        //     }
        // }

        stage('git checkout deploy branch') {
            steps {
                sh 'git checkout deploy-development'
            }
        }

        stage('copy deploy file from master') {
            steps {
                sh 'rm -rf deploy'
                sh 'git checkout main -- deploy'
            }
        }

        stage('k8s config update') {
            steps {
                dir('deploy/overlays/development') {
                    sh 'mkdir -p output'
                    sh 'kustomize edit set image argocd-test-app=argocd-test-app-dev:${BUILD_NUMBER}'
                    sh 'kustomize build . > output/deploy.yaml'
                }
            }
        }

        stage('k8s config commit and push') {
            steps {
                sh 'git config --global user.email "jenkins@jinhakapply.com"'
                sh 'git config --global user.name "jenkins"'
                sh 'git add -A'
                sh 'git commit -m "update! version: ${BUILD_NUMBER}"'
                withCredentials([
                    usernamePassword(
                        credentialsId: 'sdg_git',
                        usernameVariable: 'GIT_USERNAME',
                        passwordVariable: 'GIT_PASSWORD'
                    )]) {
                    sh("git push http://$GIT_USERNAME:$GIT_PASSWORD@github.com/sdg9670/argocd-test-app.git")
                }
            }
        }

        stage('deploy') {
            steps {
                echo 'deploy skip!'
            }
        }
    }
    
    post { 
        always { 
            cleanWs()
        }
    }
}
