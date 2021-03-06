pipeline {
    agent {label 'aws-slave'}

    stages {
        stage('Clone the application repo') {
            steps {
				sh 'rm -rf *'
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
				sh 'sudo docker export --output="helloworld.tar" helloworld'
				sh 'sudo docker rm helloworld'
		    	sh 'sudo chmod 777 helloworld.tar'
				withAWS(credentials:'raghu_aws') {
					s3Upload(file:'helloworld.tar', bucket:'artifactory-docker', path:'helloworld.tar')
				}
            }
        }
        stage('Deploy') {
            steps {
                withAWS(credentials:'raghu_aws') {
					script{
						cfnDelete(stack:'hello-world')
						def api_url = cfnUpdate(stack:'hello-world', file:'hello_world.yaml')
						echo "The api can be accessed form the URL ${api_url}"
						}
					}
				sh 'rm -rf * .git'
				git branch: 'master',
				credentialsId: 'raghu_git_ssh',
				url: 'git@github.com:raghualapati/ci_build.git'
				sh 'echo ${BUILD_NUMBER} deployed on "$(date)" >> build.txt'
				sshagent(['raghu_git_ssh']){
					sh 'git add build.txt'
					sh 'git commit -m "Build_${BUILD_NUMBER}_build_info"'
					sh 'git pull git@github.com:raghualapati/ci_build.git'
					sh 'git push git@github.com:raghualapati/ci_build.git'
					}
				
            }
        }
    }
}
