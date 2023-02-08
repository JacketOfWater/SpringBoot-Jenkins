pipeline {
	agent any
	stages{
		stage('Test Application'){
			steps{
			sh 'mvn clean test'
			}
		}
		stage('Save Tests'){
			steps{
			sh 'mkdir -p /home/jenkins/Tests/${BUILD_NUMBER}_tests/'
			sh 'mv ./target/surefire-reports/*.txt /home/jenkins/Tests/${BUILD_NUMBER}_tests/'
			}
		}
		stage('War Build'){
			steps{
			sh '''
			mvn clean package
			echo 'Piers was ere'
			'''
			}
		}
		stage('Moving War'){
			steps{
			sh '''
			mkdir -p ./wars
			mv ./target/*.war ./wars/project_war.war
			'''
			}
        }
		stage('Build Docker Image'){
			steps{
			sh '''
			docker build -t jacketofwater/springdemo:latest .
			'''
			}
        }
		stage('Push Docker Image'){
			steps{
			sh '''
			docker push jacketofwater/springdemo:latest
			'''
			}
        }
		stage('Stopping Container'){
			steps{
				script {
					if ("${GIT_BRANCH}" == 'origin/main') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@34.163.242.237 << EOF
						docker rm -f javabuild
						'''
					} else if ("${GIT_BRANCH}" == 'origin/development') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@34.79.179.51 << EOF
						docker rm -f javabuild
						'''
					}
				}
			}
		}
		stage('Restart App'){
			steps{
				script {
					if ("${GIT_BRANCH}" == 'origin/main') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@34.163.242.237 << EOF
						docker run -d -p 8080:8080 --name javabuild jacketofwater/springdemo:latest
						'''
					} else if ("${GIT_BRANCH}" == 'origin/development') {
						sh '''
						ssh -i "~/.ssh/id_rsa" jenkins@34.79.179.51 << EOF
						docker run -d -p 8080:8080 --name javabuild jacketofwater/springdemo:latest
						'''
					}
				}
			}
		}

	}
}
