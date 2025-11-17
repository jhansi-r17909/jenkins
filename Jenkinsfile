pipeline {
    agent any

    environment {
        SNYK_TOKEN = credentials('synktoken')
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/jhansi-r17909/jenkins.git'
            }
        }

        stage('Build Java Project') {
            steps {
                sh '''
                    echo "Building Java Project..."
                    cd javavapp-standalone
                    mvn clean install -DskipTests
                '''
            }
        }

        stage('Install Snyk CLI') {
            steps {
                sh 'curl -sL https://snyk.io/install.sh | sh'
            }
        }

        stage('Snyk Auth') {
            steps {
                sh 'snyk auth $SNYK_TOKEN'
            }
        }

        stage('Snyk Scan') {
            steps {
                sh '''
                    mkdir -p snyk-reports

                    cd javavapp-standalone

                    snyk test --all-projects \
                        --json-file-output=../snyk-reports/snyk.json

                    snyk test --all-projects \
                        --sarif-file-output=../snyk-reports/snyk.sarif
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'snyk-reports/*', fingerprint: true
        }
    }
}
