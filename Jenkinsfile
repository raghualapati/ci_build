pipeline {
    agent any

    stages {
        stage('Clone the application repo') {
            steps {
                git branch: 'master',
                credentialsId: 'raghu_git',
                url: 'https://github.com/raghualapati/httpserver.git'
            }
        }
		stage('Build Docker image') {
            steps {
			//sh 'docker build -t raghu/app:v${BUILD_NUMBER} .'
			sh 'whoami'
            }
        }
		stage('verify the docker images') {
            steps {
				//sh 'docker images'
                sh 'echo hello world'
            }
        }
        stage('Test') {
            steps {
			
                sh 'curl -fsSL https://goss.rocks/install | sh
				export GOSS_FILES_STRATEGY=cp
				dgoss run -d -p 8020:8080 raghu/app:v${BUILD_NUMBER}'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}