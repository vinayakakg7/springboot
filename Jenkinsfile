pipeline{
    agent any

    tools {
        maven 'maven'
        jdk 'Java'
    }

    environment{
        GIT_REPO = 'https://github.com/vinayakakg7/springboot.git'
        GIT_BRANCH = 'main'
        DOCKER_NAMESPACE = "vinayakakg7"
    }
    stages {
        stage('Clone Git repository') {
            steps {
                git branch: GIT_BRANCH, url: GIT_REPO
            }
        }
    stage('Build and test using Maven') {
            steps {
                bat 'mvn clean install -DskipTests=true'
            }
        }
   

//	stage('Remove Docker images'){
//      steps{
 //       script{
//				def imageTag = "${DOCKER_NAMESPACE}/${env.JOB_NAME}:${env.BUILD_ID}"
            //     bat "docker rmi -f ${DOCKER_NAMESPACE}/${env.JOB_NAME}:12"
               // bat "docker rmi ${DOCKER_NAMESPACE}/${env.JOB_NAME}:16"
//				bat "docker rmi ${DOCKER_NAMESPACE}/${env.JOB_NAME}:V1"
//				bat "docker rmi ${DOCKER_NAMESPACE}/${env.JOB_NAME}:latest"
//				}
//			}
//		}
    stage('Docker image build'){
      steps{
        script{
			def imageTag = "${DOCKER_NAMESPACE}/${env.JOB_NAME}:${env.BUILD_ID}"
			 bat "docker build -t ${imageTag} -f Dockerfile ."
             bat "docker image tag ${DOCKER_NAMESPACE}/${env.JOB_NAME}:${env.BUILD_ID} ${DOCKER_NAMESPACE}/${env.JOB_NAME}:V1"
			 bat "docker image tag ${DOCKER_NAMESPACE}/${env.JOB_NAME}:${env.BUILD_ID} ${DOCKER_NAMESPACE}/${env.JOB_NAME}:latest"

        }
      }
    }
     stage('Push image to DockerHub'){
      steps{
        script{
          withCredentials([string(credentialsId: 'Docker_Credenti' , variable: 'Docker_Credent')]) {
            bat "docker login -u vinayakakg7 -p ${Docker_Credent}"
            bat "docker image push ${DOCKER_NAMESPACE}/${env.JOB_NAME}:V1"
            bat "docker image push ${DOCKER_NAMESPACE}/${env.JOB_NAME}:latest"
          }
        }
      }
     }

      stage('Deploy to Minikube') {
            steps {
                script {
                    kubeconfig(credentialsId: 'Kubecred', serverUrl: '') {
                        // Apply the Kubernetes YAML file
                     bat "kubectl apply -f deploymentservice.yml"
                }
                }
                
                
            }
        }
     
}
}