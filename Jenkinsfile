pipeline {
    agent any

    environment {
        GITHUB_REPO = 'https://github.com/DHARMIKR/MYPmanager.git'
        GIT_BRANCH = 'main'
        APP_PORT = '80'  // Port your Python app runs on
        SONARQUBE_SCANNER_HOME = tool name: 'sonar', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: "${GIT_BRANCH}", url: "${GITHUB_REPO}"
            }
        }

        stage('Source Composition Analysis'){
            steps{
                sh 'rm results.txt || true'
                sh 'pip install safety'
                sh 'safety check -r /var/lib/jenkins/workspace/mypmanager/requirements.txt > results.txt || true'
                sh 'cat results.txt'
            }
        }
        
        stage('SAST') {
            steps {
                withSonarQubeEnv('sonar') { // Use the configured SonarQube server
                    sh "${SONARQUBE_SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=devsecops1 -Dsonar.sources=. -Dsonar.language=python -Dsonar.sourceEncoding=UTF-8 -Dsonar.login=sqa_b521b254d233f95eafa3f949a296fb4a120923b3"
                }
            }
        }

        stage('Start Python Server') {
            steps {
                script {
                    // Start the Python server in the background
                    sh 'nohup python3 main.py &'
                    // Wait for the server to start
                    sleep 10
                }
            }
        }

        stage('DAST') {
            steps {
                sh 'docker run -t ictu/zap2docker-weekly zap-baseline.py -t http://127.0.0.1:5000/api/ || true'
            }
        }

        stage('Stop Python Server') {
            steps {
                script {
                    // Stop the Python server
                    sh 'pkill -f main.py || true'
                }
            }
        }
    }

}
