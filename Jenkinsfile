pipeline {
   environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerid')
        TIMESTAMP = new Date().format("yyyyMMdd_HHmmss")
    }
   agent any

   stages {
      stage('Build Image') {
         steps {
            script{
               sh 'rm -rf *.war'
               sh 'jar -cvf 645HW1.war -C src/main/webapp/ .'
               //sh 'echo ${BUILD_TIMESTAMP}'
               sh "docker build -t sairohithpasham/studentsurvey:${env.TIMESTAMP} ."
               sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
         }
      }

      stage('Push Image') {
         steps {
            script{
                  sh "docker push sairohithpasham/studentsurvey:${env.TIMESTAMP}"
            }
         }
      }

      stage('Update Deployment') {
         steps {
            script{
               sh "kubectl set image deployment/hw container-0=sairohithpasham/studentsurvey:${env.TIMESTAMP}"
            }
         }
      }
      
      stage('Update LoadBalancer') {
         steps {
            script{
               sh 'kubectl rollout restart deploy hw -n default'
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
