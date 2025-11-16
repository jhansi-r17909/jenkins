pipeline {
    agent any

    environment {
        REPO_URL   = 'https://github.com/jhansi-r17909/jenkins.git'
        PROJECT_DIR = 'javaapp-standalone'
        DEPLOY_PATH = '/opt/deployments/myapp'   // change this to your desired path
    }

    stages {

        stage('Checkout') {
            steps {
                git url: "${env.REPO_URL}", branch: 'main'
            }
        }

        stage('Build') {
            steps {
                dir("${env.PROJECT_DIR}") {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Run Tests') {
            steps {
                dir("${env.PROJECT_DIR}") {
                    sh 'mvn test'
                }
            }
        }

        stage('Deploy Artifact') {
            steps {
                echo "Deploying artifact to ${env.DEPLOY_PATH}"

                sh '''
                    mkdir -p ${DEPLOY_PATH}
                    ARTIFACT=$(ls ${PROJECT_DIR}/target/*.jar ${PROJECT_DIR}/target/*.war 2>/dev/null | head -n 1)

                    if [ -z "$ARTIFACT" ]; then
                        echo "No artifact found inside ${PROJECT_DIR}/target/"
                        exit 1
                    fi

                    cp "$ARTIFACT" ${DEPLOY_PATH}/
                    echo "Artifact copied to ${DEPLOY_PATH}"
                '''
            }
        }
    }

    post {
        success { echo 'Pipeline completed successfully.' }
        failure { echo 'Pipeline failed — check logs.' }
    }
}
