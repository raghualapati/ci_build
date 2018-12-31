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
			git url: "ssh://jenkins@your-git-repo:12345/your-git-project.git",
			credentialsId: 'jenkins_ssh_key',
			branch: master	
			sh 'git tag -a tagName -m "${BUILD_NUMBER}_build_complete"'
			sh 'git commit -am "Merged develop commit to master"'
			sh 'git push origin master'
            }
        }
    }
}