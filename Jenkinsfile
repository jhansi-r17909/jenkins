pipeline {
    agent any

    environment {
        SNYK_TOKEN = credentials('synktoken')
    }

    stages {

        stage('Checkout Source') {
            steps {
                git branch: 'main', url: 'https://github.com/jhansi-r17909/jenkins.git'
            }
        }

        stage('Build Java Project') {
            steps {
                sh '''
                    cd javavapp-standalone
                    mvn clean install -DskipTests
                '''
            }
        }

        stage('Install Snyk') {
            steps {
                sh 'curl -sL https://snyk.io/install.sh | sh'
            }
        }

        stage('Authenticate Snyk') {
            steps {
                sh 'snyk auth $SNYK_TOKEN'
            }
        }

        stage('Run Snyk Security Scan') {
            steps {
                sh '''
                    mkdir -p snyk-reports
                    cd javavapp-standalone

                    snyk test --json-file-output=../snyk-reports/snyk.json
                    snyk test --sarif-file-output=../snyk-reports/snyk.sarif
                '''
            }
        }
    } // end stages

    post {
        always {
            script {
                echo "Checking and archiving Snyk reports..."

                if (fileExists('snyk-reports')) {
                    archiveArtifacts artifacts: 'snyk-reports/*', fingerprint: true
                } else {
                    echo "⚠️ snyk-reports folder NOT found!"
                }
            }
        }
    }
}
