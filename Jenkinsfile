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
				sh 'sudo docker build -t raghu/app:v${BUILD_NUMBER} .'
            }
        }
        stage('Test') {
            steps {
				sh 'sudo /usr/local/bin/dgoss run -p 8020:8080 raghu/app:v${BUILD_NUMBER}'
            }
        }
        stage('Deploy') {
            steps {
                withAWS(credentials:'raghu_aws') {
					cfnUpdate(stack:'my-stack', file:'hello_world.yaml', pollInterval:1000)}
				credentialsId: 'raghu_git'
				sh 'echo ${BUILD_NUMBER} Deployed on $(date) >> buildinfo.txt'		
				sh 'git push origin master https://github.com/raghualapati/httpserver.git'

            }
        }
    }
}