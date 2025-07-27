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
                git 'https://github.com/your-org/operator-repo.git'
            }
        }

        stage('Deploy Operator') {
            steps {
                script {
                    // If Helm is used
                    sh """
                        helm upgrade --install my-operator ./helm-chart \\
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
