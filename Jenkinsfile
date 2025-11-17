pipeline {
    agent any

    environment {
        SNYK_TOKEN = credentials('SNYK_TOKEN')
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

        stage('Install Snyk CLI') {
            steps {
                sh 'curl -sL https://snyk.io/install.sh | sh'
            }
        }

        stage('Authenticate Snyk') {
            steps {
                sh 'snyk auth $SNYK_TOKEN'
            }
        }

        stage('Run Snyk Scan') {
            steps {
                sh '''
                    mkdir -p snyk-reports
                    cd javavapp-standalone
                    
                    snyk test --json-file-output=../snyk-reports/snyk.json
                    snyk test --sarif-file-output=../snyk-reports/snyk.sarif
                '''
            }
        }
    }

    post {
        always {
            sh '''
                if [ -d "snyk-reports" ]; then
                    echo "Archiving reports..."
                else
                    echo "No snyk-reports folder found."
                fi
            '''

            archiveArtifacts artifacts: 'snyk-reports/*', allowEmptyArchive: true
        }
    }
}
