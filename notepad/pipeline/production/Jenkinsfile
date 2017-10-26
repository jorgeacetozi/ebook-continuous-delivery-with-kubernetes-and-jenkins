podTemplate(label: 'production', 
  containers: [
    containerTemplate(name: 'kubectl', image: 'jorgeacetozi/kubectl:1.7.0', ttyEnabled: true, command: 'cat')
  ],
  envVars: [
    secretEnvVar(key: 'DOCKERHUB_USERNAME', secretName: 'dockerhub-username-secret', secretKey: 'USERNAME')
  ])
{
  node ('production') {
    def image_name = "notepad"

    checkout scm

    stage('Deploy release version to Production environment using RollingUpdate strategy') {
      container('kubectl') {
        sh """
          sed -i "s/NOTEPAD_CONTAINER_IMAGE/${DOCKERHUB_USERNAME}\\/${image_name}:${RELEASE_VERSION}/" ./notepad/k8s/production/notepad-production-deployment.yaml

          kubectl apply -f notepad/k8s/production/ -l app=mysql
          sleep 20

          kubectl apply -f notepad/k8s/production/ -l app=notepad
          kubectl rollout status deployment notepad-deployment-production

          kubectl delete all -l track=canary

          kubectl get service notepad-service-production
          kubectl get endpoints notepad-service-production
        """
      }
    }
  }
}
