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
				withCredentials([usernamePassword(credentialsId: 'raghu_git', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
					sh 'git tag -a ${BUILD_NUMBER}_success -m "update"'
					sh 'git push ${GIT_USERNAME}:${GIT_PASSWORD}@https://github.com/raghualapati/httpserver.git'
				}
            }
        }
    }
}