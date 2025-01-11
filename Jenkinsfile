pipeline{
    agent any
    stages{
        stage('Checkout scm'){
            steps{
                sh 'echo "checkout done"'
            }
        }
        stage('test and build'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('static code analysis'){
           environment {
                SONAR_URL = "http://65.2.177.135:9000"
            }
            steps{
                withCredentials([string(credentialsId: 'sonar', variable: 'sonar')]) {
                    sh 'mvn sonar:sonar -Dsonar.login=$sonar -Dsonar.host.url=${SONAR_URL}'
                }      
            }
        }
        stage('Dockerimage'){
            steps{
                sh 'docker build -t pratikkambl3/spring-app .'
            }
        }
        stage('push image'){
            environment {
                DOCKER_IMAGE = "pratikkambl3/spring-app:${BUILD_NUMBER}"
                // DOCKERFILE_LOCATION = "java-maven-sonar-argocd-helm-k8s/spring-boot-app/Dockerfile"
                REGISTRY_CREDENTIALS = credentials('docker-login')
            }   
            steps{
                withCredentials([usernamePassword(credentialsId: 'docker-login', passwordVariable: 'pwdd', usernameVariable: 'user')]) {
                    sh 'echo $pwdd | docker login -u $user --password-stdin '
                    sh 'docker push ${DOCKER_IMAGE}'
                }
            }
        }
    }
}
