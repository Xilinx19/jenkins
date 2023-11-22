pipeline {
    agent any
    stages {
        stage('Build Maven') {
            steps {
                sh 'pwd'
                sh 'mvn clean install package'
            }
        }
        stage ('Copy Artifacts') {
            steps{
                sh 'pwd'
                sh 'cp -r target/*.jar docker'
            }
        }
        stage ('Run Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage ('Build Docker Image') {
            steps{
                script {
                    def customImage = docker.build("xilinx19/petclinic:${env.BUILD_NUMBER}","./docker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    customImage.push()  
                    
                    }
                                        
                }
            }
        }
        stage('build on k8s'){
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']) {
                    sh 'pwd'
                    sh 'cp -R helm/*.'
                    sh 'ls -lrth'
                    sh 'pwd'
                    sh '/usr/local/bin/helm upgrade --install petclinic-app petclinic --set image.repository=ashaik65/petclinic --st image.tag=${BUILD_NUMBER}'
                }
            }
        }
    }
}