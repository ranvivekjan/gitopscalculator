@Library('global-shared-library') _
pipeline {
    agent any
    stages {
         stage('Check Out') {
            steps {
                checkOut(
                    branch: "master",
                    url: "https://github.com/he329178/gitopscalculator.git"
                )
            }
        }
        stage('Maven Version') {
            steps {
                mavenVersion()    
            }
        }
        stage('Sonar') {
            steps {
                sonarScan()
            }
        }
        stage('RunTest Cases') {
            steps {
                runTests()
            }
        }
        stage('Publich Test Reports') {
            steps {
                publishReports()
            }
        }
        stage('Build Code') {
            steps {
                buildCode()
            }
        }
        stage ('Artifact') {
            steps {
                artifacts()
            }
        }
         stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t papannah/jenkins .'
                }
            }
        }
         stage('Push image to DockerHub'){
            steps{
                script{
                   withCredentials([string(credentialsId: 'papannah', variable: 'dockerhubpass')]) {
                       sh 'docker login -u papannah -p ${dockerhubpass}'
                   }
                   sh 'docker push papannah/jenkins'
                }
            }
        }
         stage('Deploy App on k8s') {
		    steps {
			    sshagent(['k8s']) {
				    sh "scp -o StrictHostKeyChecking=no deployment.yml ubuntu@65.0.55.243:/home/ubuntu"
				    script {
					    try{
						    sh "ssh ubuntu@65.0.55.243 kubectl create -f ./deployment.yml"
					    }catch(error){
						    sh "ssh ubuntu@65.0.55.243 kubectl apply -f ./deployment.yml"
					    }
				    }
			    }  
		    }
	    }
    }
}
