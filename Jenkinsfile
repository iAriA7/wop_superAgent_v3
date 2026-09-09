pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        buildDiscarder(logRotator(numToKeepStr: '20'))
        skipDefaultCheckout(true)
    }

    environment {
        PIPELINE_ARTIFACT = 'build-metadata.tar.gz'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Baseline validation') {
            steps {
                sh '''
                    set -eu

                    test -f README.md
                    test -f LICENSE
                    test -f Jenkinsfile
                '''
            }
        }

        stage('Precision runtime gate') {
            steps {
                sh '''
                    set -eu

                    grep -qi "NVIDIA NeMo" README.md
                    grep -qi "runtime backbone" README.md
                    grep -qi "Jenkins CI/CD pipeline" README.md
                '''
            }
        }

        stage('Package metadata') {
            steps {
                sh '''
                    set -eu

                    tar -czf "${PIPELINE_ARTIFACT}" README.md LICENSE Jenkinsfile
                '''

                archiveArtifacts artifacts: "${PIPELINE_ARTIFACT}", fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'Precision-centric Jenkins pipeline completed successfully.'
        }

        failure {
            echo 'Jenkins pipeline failed; inspect the validation gate output.'
        }

        always {
            deleteDir()
        }
    }
}
