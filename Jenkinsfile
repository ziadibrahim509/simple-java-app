pipeline {
    agent any

    tools {
        maven 'M3916'
    }
    
    environment {
        DOCKER_REGISTRY_USER = 'ziadibrahim123' 
        IMAGE_NAME           = 'simple-java-app'
        IMAGE_TAG            = "${BUILD_NUMBER}"
        DOCKER_HOST          = 'tcp://172.17.0.1:2375'
    }

    stages {
        stage('1. Fetch Code') {
            steps {
                echo 'Fetching Code from Your GitHub Repo...'
                git branch: 'main', url: 'https://github.com/ziadibrahim509/simple-java-app''
            }
        }

        stage('2. Build') {
            steps {
                echo 'Building Java Application using Maven...'
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('3. Test') {
            steps {
                echo 'Running Unit Tests...'
                sh 'mvn test'
            }
        }

        stage('4. Push (Docker Image)') {
            steps {
                echo 'Building Docker Image and Pushing to Docker Hub...'
                sh "docker build -t ziadibrahim123/simple-java-app:\${BUILD_NUMBER} ."
                sh "docker tag ziadibrahim123/simple-java-app:\${BUILD_NUMBER} ziadibrahim123/simple-java-app:latest"
                
                sh "docker login -u ziadibrahim123 -p dckr_pat_ByTlVMkxJltKyu4KxJlZ5LFXXSo"
                
                sh "docker push ziadibrahim123/simple-java-app:\${BUILD_NUMBER}"
                sh "docker push ziadibrahim123/simple-java-app:latest"
            }
        }

        stage('5. Deploy') {
            steps {
                echo 'Deploying Application to Production...'
                sh '''
                docker stop simple-java-app-running || true
                docker rm simple-java-app-running || true
                docker run -d -p 8081:8080 --name simple-java-app-running ziadibrahim123/simple-java-app:latest
                '''
                echo 'Application is live on port 8081!'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up docker login session...'
            sh 'docker logout || true'
        }
    }
}
