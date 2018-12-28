pipeline {
    agent {label 'aws-slave'}

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
			sh 'sudo systemctl start docker'
			sh 'sudo docker build -t raghu/app:v${BUILD_NUMBER} .'
            }
        }
		stage('verify the docker images') {
            steps {
				sh 'sudo docker images'
            }
        }
        stage('Test') {
            steps {
			
                sh 'curl -fsSL https://goss.rocks/install | sudo sh'
				sh 'export GOSS_FILES_STRATEGY=cp'
				sh 'export GOSS_PATH=/usr/local/bin/goss'
				sh 'wget https://github.com/raghualapati/httpserver/raw/master/goss.yaml'
				sh 'sudo /usr/local/bin/dgoss run -d -p 8020:8080 raghu/app:v${BUILD_NUMBER}'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}