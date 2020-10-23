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
                docker { image 'busybox' }
            }
            steps {
                sh 'echo docker build'
                docker.withTool('docker') {
                        repoId = "bensonre/sample"
                        image = docker.build(repoId)
                        docker.withRegistry("https://registry.hub.docker.com", "docker") {
                            image.push()
                        }
                }
            }
        }
        stage('docker push') {
            agent {
                docker { image 'busybox' }
            }
            steps {
                sh 'echo docker push'
            }
        }
        stage('app deploy') {
            agent {
                docker { image 'busybox' }
            }
            steps {
                sh 'echo kube deploy'
            }
        }
    }
}
