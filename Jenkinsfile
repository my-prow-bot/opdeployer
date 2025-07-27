pipeline {
    agent any

    parameters {
        string(name: 'OPERATOR_VERSION', defaultValue: '1.0.0', description: 'Operator version')
        choice(name: 'NAMESPACE', choices: ['default', 'operators'], description: 'Target namespace')
    }

    environment {
        KUBECONFIG = credentials('kubeconfig-jenkins') // Jenkins credential containing kubeconfig
    }

    stages {
        stage('Install Helm') {
                 steps {
                sh '''
                  curl -LO https://get.helm.sh/helm-v3.18.4-linux-amd64.tar.gz
                  tar -zxvf helm-v3.18.4-linux-amd64.tar.gz
                  mv linux-amd64/helm /usr/local/bin/helm || cp linux-amd64/helm ./helm
                  chmod +x ./helm
                  ./helm version
                '''
            }
        }
        stage('Configure Git') {
            steps {
                sh '''
                  git config user.email "jenkins@ci.local"
                  git config user.name "Jenkins CI"
                '''
            }
        }
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/my-prow-bot/opdeployer.git'
            }
        }

        stage('Deploy Operator') {
            steps {
                script {
                    // If Helm is used
                    sh """
                        ./helm upgrade --install my-operator ./helm-chart \\
                            --namespace ${params.NAMESPACE} \\
                            --set image.tag=${params.OPERATOR_VERSION}
                    """
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                sh """
                    kubectl -n ${params.NAMESPACE} rollout status deployment/my-operator
                """
            }
        }
    }
}
