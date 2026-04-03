pipeline{
    agent any

    environment {
        SONARQUBE_SERVER='SonarQubeServer'
        IMAGE_NAME='alphaimage'

        PATH = "/opt/sonar-scanner/bin:$PATH"

    }

    stages{
        stage("checkout"){
            steps{
                git url: "https://github.com/Gokulraja2004/nginx.git"
            }

        }

        stage("tag iamge") {
            steps{
                script{
                    def shortHash=sh(
                        script: "git rev-parse --short HEAD",
                        returnStdout:true
                    ).trim()
                    env.IMAGE_TAG="${BUILD_NUMBER}-${shortHash}"
                    echo "Using image tag: ${env.IMAGE_TAG}"
                }
            }
        }
        stage('sonarqube analysis'){
            steps{
                withSonarQubeEnv("${SONARQUBE_SERVER}"){
                    sh 'sonar-scanner'
                }
            }
        }
        stage('build stage'){
            steps{
                echo "Using updated image tag ${IMAGE_NAME}-${IMAGE_TAG}"
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
                sh 'docker tag ${IMAGE_NAME}:${IMAGE_NAME}:latest'
            }
        }
        stage('Image Scan'){
            steps{
                sh "trivy image ${IMAGE_NAME}:${IMAGE_TAG} > report.txt"
            }
        }
     
    }
}