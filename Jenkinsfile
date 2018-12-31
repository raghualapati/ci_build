pipeline {
    agent {label 'aws-slave'}

    stages {
        stage('Clone the application repo') {
            steps {
                git branch: 'master',
                credentialsId: 'raghu_git_ssh',
                url: 'git@github.com:raghualapati/httpserver.git'
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
				sh 'echo ${BUILD_NUMBER} deployed on "$(date)" >> build.txt'
				git(changelog: false,
					credentialsId: 'raghu_git_ssh',
					poll: fasle,
					branch: 'master',
					url: 'git@github.com:raghualapati/httpserver.git')
				sshagent([raghu_git_ssh]){
					sh 'git add build.txt'
					sh 'git commit -m "${BUILD_NUMBER}_build_info"'
					sh 'git config remote.origin.url git@github.com:raghualapati/httpserver.git'
					sh 'git pull ssh://git@github.com:raghualapati/httpserver.git'
					sh 'git push ssh://git@github.com:raghualapati/httpserver.git'
					}
				
            }
        }
    }
}