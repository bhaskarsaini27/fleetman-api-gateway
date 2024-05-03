pipeline {
   agent any

   environment {
     // You must set the following environment variables
     // ORGANIZATION_NAME
     // YOUR_DOCKERHUB_USERNAME (it doesn't matter if you don't have one)

     SERVICE_NAME="fleetman-api-gateway"
     ORGANIZATION_NAME="bhaskarsaini27"
     YOUR_DOCKERHUB_USERNAME="sainibha"
     REPOSITORY_TAG="${YOUR_DOCKERHUB_USERNAME}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${BUILD_ID}"
   }

   stages {
      stage('Preparation') {
         steps {
            cleanWs()
            git credentialsId: 'GitHub', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}"
         }
      }
      stage('Package Build') {
         steps {
            sh '''mvn clean package'''
         }
      }

      stage('Image Build') {
         steps {
           sh 'docker image build -t ${REPOSITORY_TAG} .'
         }
      }
      stage('Push Image to DockerHub') {
         steps {
           sh 'docker push ${REPOSITORY_TAG} .'
         }
      }
      stage('Deploy to Cluster') {
          steps {
            withCredentials([
            string(credentialsId: 'my_kubernetes', variable: 'api_token')
            ]) {
             //sh 'envsubst < ${WORKSPACE}/deploy.yaml | /usr/local/bin/kubectl apply -f -'
             sh 'envsubst < ${WORKSPACE}/deploy.yaml | kubectl --token $api_token --server https://192.168.49.2:8443  --insecure-skip-tls-verify=true apply -f -'
               }
             
          }
      }
   }
}
