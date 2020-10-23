pipeline {
    agent none
    stages {
        stage('build') {
            agent {
                docker { image 'gradle' }
            }
            steps {
                sh 'chmod +x gradlew && ./gradlew build'
            }
        }
        stage('sonarqube') {
            agent {
                docker { image 'gradle' }
            }
            steps {
                withSonarQubeEnv("Sonarcloud")
                {
                    sh "chmod +x gradlew && ./gradlew sonarqube"
                   // sleep(10)
                }
                /*timeout(time: 1, unit: 'HOURS') {
                        waitForQualityGate abortPipeline: true
                }*/
            }
        }
        stage('docker build') {
            agent {
                docker { image 'docker' }
            }
            steps {
                sh 'echo docker build'
                script{
                    withDockerRegistry(credentialsId: 'docker', url: 'https://registry.hub.docker.com') {
                    sh 'docker build -t bensonre/interview .'
                    }
                }
            }
        }
        stage('docker push') {
            agent {
                docker { image 'docker' }
            }
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker', url: 'https://registry.hub.docker.com') {
                        sh 'docker push bensonre/interview'
                    }
                }
                
            }
        }
        stage('app deploy') {
            agent {
                // change to some image with kubernetes
                docker { image 'busybox' }
            }
            steps {
                sh 'kubectl rollout restart deployment/sample-spring-boot'
            }
        }
    }
}
