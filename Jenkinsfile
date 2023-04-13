pipeline {
   environment {
        DOCKERHUB_CREDENTIALS = credentials('dockid')
        TIMESTAMP = new Date().format("yyyyMMdd_HHmmss")
    }
   agent any

   stages {
      stage('Build Image') {
         steps {
            script{
               sh 'rm -rf *.war'
               sh 'jar -cvf Survey.war -C src/main/webapp/ .'
               //sh 'echo ${BUILD_TIMESTAMP}'
               sh "docker build -t ramiyappan/studentsurvey:${env.TIMESTAMP} ."
               sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
         }
      }

      stage('Push Image') {
         steps {
            script{
                  sh "docker push ramiyappan/studentsurvey:${env.TIMESTAMP}"
            }
         }
      }

      stage('Update Deployment') {
         steps {
            script{
               sh "kubectl set image deployment/newdeployment container-0=ramiyappan/studentsurvey:${env.TIMESTAMP}"
            }
         }
      }
      
      stage('Update LoadBalancer') {
         steps {
            script{
               sh 'kubectl rollout restart deploy newdeployment -n default'
            }
         }
      }
   }
      
      post {
	  always {
		sh 'docker logout'
	  }
      }    
}
