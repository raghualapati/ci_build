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
        stage('Test the image') {
            steps {
				sh 'sudo /usr/local/bin/dgoss run -p 8020:8080 raghu/app:v${BUILD_NUMBER}'
            }
        }
		stage('Push image to S3') {
            steps {
				sh 'sudo docker run -itd -p 8080:8080 --name helloworld raghu/app:v${BUILD_NUMBER}'
				sh 'sudo docker stop helloworld'
				sh 'sudo docker export --output="helloworld_${BUILD_NUMBER}.tar" helloworld'
				sh 'sudo docker rm helloworld'
				withAWS(credentials:'raghu_aws') {
					s3Upload(file:'helloworld_${BUILD_NUMBER}.tar', bucket:'artifactory-docker', path:'helloworld_${BUILD_NUMBER}.tar')
				}
            }
        }
        stage('Deploy') {
            steps {
                withAWS(credentials:'raghu_aws') {
					cfnUpdate(stack:'my-stack', file:'hello_world.yaml', pollInterval:1000)}
				sh 'echo ${BUILD_NUMBER} deployed on "$(date)" >> build.txt'
				sshagent(['raghu_git_ssh']){
					sh 'git add build.txt'
					sh 'git commit -m "Build_${BUILD_NUMBER}_build_info"'
					sh 'git pull git@github.com:raghualapati/httpserver.git'
					sh 'git push git@github.com:raghualapati/httpserver.git'
					}
				
            }
        }
    }
}
