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
                sh 'safety check -r /var/lib/jenkins/workspace/mypmanager/requirements.txt > results.txt'
                sh 'cat results.txt'
            }
        }
    }

}
