pipeline {
    agent {label 'aws-slave'}

    stages {
        stage('Clone the application repo') {
            steps {
                git branch: 'master',
                credentialsId: 'raghu_git_ssh',
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
				withCredentials([usernamePassword(credentialsId: 'raghu_git', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]){
				sh 'echo ${BUILD_NUMBER} deployed on "$(date)" >> build.txt'
				sh 'echo "${GIT_PASSWORD}"'
				sh 'git config --global user.name "${GIT_USERNAME}"'
				sh 'git add build.txt'
				sh 'git commit -m "${BUILD_NUMBER}_build_info"'
				sh 'git push origin master'
				}
            }
        }
    }
}