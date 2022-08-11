pipeline {

	agent any

	environment {
		DOCKERHUB_CREDENTIALS = credentials('saud-dockerhub-token')
		AWS_ACCESS_KEY_ID     = credentials('saud-aws-secret-key-id')
  		AWS_SECRET_ACCESS_KEY = credentials('saud-aws-secret-access-key')
		ARTIFACT_NAME = 'Dockerrun.aws.json'
		AWS_S3_BUCKET = 'saud-belt2-artifacts-123456'
		AWS_EB_APP_NAME = 'saud-Belt2-eb'
        AWS_EB_ENVIRONMENT_NAME = 'Docker11-env'
        AWS_EB_APP_VERSION = "${BUILD_ID}"
        AWS_REGION = 'us-east-1'
	}

	stages {

		stage('Build') {
			steps {
				sh 'docker build -t saudsaadotb/runaway:latest .'
			}
		}

		stage('Login') {
			steps {
				sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
			}
		}

		stage('Push') {
			steps {
				sh 'docker push saudsaadotb/runaway'
			}
		}

        stage('Deploy') {
            steps {
                sh 'aws configure set region $AWS_REGION'
                sh 'aws elasticbeanstalk create-application-version --application-name $AWS_EB_APP_NAME --version-label $AWS_EB_APP_VERSION --source-bundle S3Bucket=$AWS_S3_BUCKET,S3Key=$ARTIFACT_NAME'
                sh 'aws elasticbeanstalk update-environment --application-name $AWS_EB_APP_NAME --environment-name $AWS_EB_ENVIRONMENT_NAME --version-label $AWS_EB_APP_VERSION'
            }
	}
    }
	post {
		always {
			sh 'docker logout'
		}
	}
}