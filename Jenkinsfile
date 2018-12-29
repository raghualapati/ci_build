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
			sh 'sudo usermod -aG docker ec2-user'
			sh 'id'
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
				sh 'sudo echo "export GOSS_FILES_STRATEGY=cp" >> /etc/environment'
				sh 'sudo echo "export GOSS_PATH=/usr/local/bin/goss" >> /etc/environment'
				sh 'whoami'
				sh 'sudo /usr/local/bin/dgoss run -p 8020:8080 raghu/app:v${BUILD_NUMBER}'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}