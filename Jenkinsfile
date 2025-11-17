pipeline {
    agent any

    environment {
        SONAR = credentials('sonarqube-token')
        SNYK = credentials('SYNK_TOKEN')
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/jhansi-r17909/jenkins.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('sonar-global') {
                    sh """
                        mvn sonar:sonar \
                        -Dsonar.login=${SONAR}
                    """
                }
            }
        }

        stage('Snyk Scan') {
            steps {
                sh """
                    snyk auth ${SNYK}
                    snyk test
                """
            }
        }

        stage('Deploy to Server 13.232.59.70') {
            steps {
                sshagent(['remote-server-ssh']) {

                    sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@13.232.59.70 "mkdir -p /home/ubuntu/app"
                    """

                    sh """
                        scp -o StrictHostKeyChecking=no target/*.jar ubuntu@13.232.59.70:/home/ubuntu/app/
                    """
                }
            }
        }

        stage('Cleanup Workspace') {
            steps {
                echo "Cleaning workspace..."
                sh 'rm -rf target || true'
            }
        }
    }

    post {
        always {
            echo "Pipeline completed."
        }
    }
}
