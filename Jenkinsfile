pipeline {
    agent any
    stages {
    	stage('Build') 	{
			steps {
        		sh './gradlew -b build.gradle clean build'
			}
    	}
    	stage('parallel stages') {
    		parallel {
    			stage('Archival') {
				    steps {
        				archiveArtifacts 'build/libs/*.jar'
				    }
    			}
                stage('Test cases') {
                    steps {
                        junit 'build/test-results/test/*.xml'
				    }
                }
            }
        }
        stage('Build Image') {
            steps {
                sh '''
                    docker build --no-cache -t product-catalog-image:latest .
                    docker tag product-catalog-image:latest mysanreg.azurecr.io/mysanrepo/product-catalog-image:v${BUILD_NUMBER}
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh '''
                    docker login --username MySanReg --password PvCTXlR0G=knfVVldM26S2VuirKIU+00 mysanreg.azurecr.io
                    docker push mysanreg.azurecr.io/mysanrepo/product-catalog-image:v${BUILD_NUMBER}
                '''
            }
        }
    }

    post {
        always {
            notify('started')
        }
        failure {
            notify('err')
        }
        success {
            notify('success')
        }
    }    
}

def notify(status){
    emailext (
    to: "s.surya@ucas.ac.uk",
    subject: "${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
    body: """<p>${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':</p>
        <p>Check console output at <a href='${env.BUILD_URL}'>${env.JOB_NAME}  [${env.BUILD_NUMBER}]</a></p>""",
    )
}
