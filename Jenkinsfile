pipeline {
	agent any

    environment {
        registry = "matotouraki/demo-devsecops"
        registryCredential = 'docker-hub'
    }

	stages {
		stage('GitHub') {
			steps {
			    // Get some code from a GitHub repository
				git(url: "https://github.com/amtouraki/sprint_boot_tdd.git", branch: "main", changelog: true, poll: true)
			}
		}

		stage('Build Artifact') {
			steps {
				sh "mvn clean package -DskipTests=true"
				archive 'target/*.jar'
				//so that they can be downloaded later
			}
		}

		stage('Unit Tests - JUnit and Jacoco') {
			steps {
				sh "mvn test"
			}
			post{
			    always {
			        junit 'target/surefire-reports/*.xml'
			        jacoco execPattern: 'target/jacoco.exec'
			    }
			}
		}

        stage('Docker Build and Push') {
            steps {
                withDockerRegistry([credentialsId: "docker-hub", url: ""]) {
                    sh 'printenv'
                    sh 'docker build -t $registry:$BUILD_NUMBER .'
                    sh 'docker push $registry:$BUILD_NUMBER'
                }
            }
        }

        stage('Remove Unused docker image') {
            steps{
                sh "docker rmi $registry:$BUILD_NUMBER"
            }
        }
	}
}