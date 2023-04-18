pipeline{
    agent{
        label "jenkins-agent"
    }
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    environment {
        APP_NAME = "complete-prodcution-e2e-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "smartcloud2022"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}.${BUILD_NUMBER}"
        APP_NAME = "complete-prodcution-e2e-pipeline"
        JENKINS_API_TOKEN = credentials('JENKINS_API_TOKEN')
    }
    stages{
        stage("Cleanup WorkSpace"){
            steps{
                cleanWs()
            }
        }
        stage("Checkout from SCM"){
            steps{
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/smartobi/complete-prodcution-e2e-pipeline.git'
            }
        }

        stage("Builid Application"){
            steps{
                sh "mvn clean package"
            }
        }

        stage("Test Application"){
            steps{
                sh "mvn test"
            }
        }

        stage("SonarQube Analysis"){
            steps{
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-sonar-token') {
                        sh "mvn sonar:sonar"
                    }
                
            }
        }
        }

        stage("Quality GAte Analysis"){
            steps{
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonar-token'   
                    }
        }
    }

    stage("Build  Docker Image"){
            steps{
                
                script(sandbox:false) {
                    
                    docker.withRegistry('',DOCKER_PASS)   {
                        docker_image = docker.build "${IMAGE_NAME}"
                    sh 'echo "========================completed now building image========================"'
                        //docker.push()
                      //  docker.push("latest")
                    }



                }

        }
    }

    stage("Docker push"){
            steps{
                script {
                    // docker.withRegistry('',DOCKER_PASS)  {
                    //     docker_image = docker.push("${IMAGE_TAG}")
                    sh 'docker image tag "${IMAGE_NAME}"  "${IMAGE_NAME}:${IMAGE_TAG}"'
                    sh 'docker push "${IMAGE_NAME}:${IMAGE_TAG}"'
                }
        }
    }

    stage("Trigger CD Pipeline"){
            steps{
                script {
                        sh "curl -v -k --user admin:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'https://cicd1.aetdevops.com/job/gitops-complete-pipeline/buildWithParameters?token=gitops-token'"               
                }
        }
    }
}
}






// pipeline{
//     agent{
//         label "jenkins-agent"
//     }
//     tools {
//         jdk 'Java17'
//         maven 'Maven3'
//     }
//     environment {
//         APP_NAME = "complete-prodcution-e2e-pipeline"
//         RELEASE = "1.0.0"
//         DOCKER_USER = "dmancloud"
//         DOCKER_PASS = 'dockerhub'
//         IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
//         IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
//         JENKINS_API_TOKEN = credentials('JENKINS_API_TOKEN')

//     }
//     stages{
//         stage("Cleanup Workspace"){
//             steps {
//                 cleanWs()
//             }

//         }
    
//         stage("Checkout from SCM"){
//             steps {
//                 git branch: 'main', credentialsId: 'github', url: 'https://github.com/dmancloud/complete-prodcution-e2e-pipeline'
//             }

//         }

//         stage("Build Application"){
//             steps {
//                 sh "mvn clean package"
//             }

//         }

//         stage("Test Application"){
//             steps {
//                 sh "mvn test"
//             }

//         }
        
//         stage("Sonarqube Analysis") {
//             steps {
//                 script {
//                     withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
//                         sh "mvn sonar:sonar"
//                     }
//                 }
//             }

//         }

//         stage("Quality Gate") {
//             steps {
//                 script {
//                     waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
//                 }
//             }

//         }

//         stage("Build & Push Docker Image") {
//             steps {
//                 script {
//                     docker.withRegistry('',DOCKER_PASS) {
//                         docker_image = docker.build "${IMAGE_NAME}"
//                     }

//                     docker.withRegistry('',DOCKER_PASS) {
//                         docker_image.push("${IMAGE_TAG}")
//                         docker_image.push('latest')
//                     }
//                 }
//             }

//         }

//         stage("Trigger CD Pipeline") {
//             steps {
//                 script {
//                     sh "curl -v -k --user admin:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'https://jenkins.dev.dman.cloud/job/gitops-complete-pipeline/buildWithParameters?token=gitops-token'"
//                 }
//             }

//         }

//     }
// }