pipeline {
    agent any

    environment {
        // 设置 Docker 镜像的标签
        DB_IMAGE1 = "luluplum/food:latest"
        DB_IMAGE2 = "luluplum/hotel:latest"
        DB_IMAGE3 = "luluplum/train:latest"
        DB_IMAGE4 = "luluplum/user:latest"
        DOCKER_CREDENTIALS_ID = '9b671c50-14d3-407d-9fe7-de0463e569d2'
        DOCKER_PASSWORD = 'luluplum'
        DOCKER_USERNAME = 'woaixuexi0326'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'luluplum', url: 'https://github.com/werwerTrain/database-2.git'
            }
        }
        stage('Build DB') {
            steps {
                script {
                    /*
                    // 查找并停止旧的容器
                    sh '''
                    CONTAINERS=$(docker ps -q --filter "ancestor=${DB_IMAGE}")
                    if [ -n "$CONTAINERS" ]; then
                        docker stop $CONTAINERS
                    fi
                    '''
            
                    // 删除停止的容器
                    sh '''
                    CONTAINERS=$(docker ps -a -q --filter "ancestor=${DB_IMAGE}")
                    if [ -n "$CONTAINERS" ]; then
                        docker rm $CONTAINERS
                    fi
                    '''
                    
                    sh '''
                    docker rmi -f ${DB_IMAGE} || true
                    '''
                    */
                    
                    // 构建前端 Docker 镜像
                    sh 'docker build -t ${DB_IMAGE1} ./Food'
                    sh 'docker build -t ${DB_IMAGE2} ./Hotel'
                    sh 'docker build -t ${DB_IMAGE3} ./Train'
                    sh 'docker build -t ${DB_IMAGE4} ./User'
                   
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // 使用凭证登录 Docker 镜像仓库
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push ${DB_IMAGE1}
                        '''
                    }
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push ${DB_IMAGE2}
                        '''
                    }
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push ${DB_IMAGE3}
                        '''
                    }
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push ${DB_IMAGE4}
                        '''
                    }
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    
                    // 应用 Kubernetes 配置
                    sh 'kubectl apply -f k8s/food-deployment.yaml'
                    sh 'kubectl apply -f k8s/hotel-deployment.yaml'
                    sh 'kubectl apply -f k8s/train-deployment.yaml'
                    sh 'kubectl apply -f k8s/user-deployment.yaml'
                }
            }
        }

        stage('Service to Kubernetes') {
            steps {
                script {
                    // 应用 Kubernetes 配置
                    sh 'kubectl apply -f k8s/food-service.yaml'
                    sh 'kubectl apply -f k8s/hotel-service.yaml'
                    sh 'kubectl apply -f k8s/train-service.yaml'
                    sh 'kubectl apply -f k8s/user-service.yaml'
                }
            }
        }

    }

    post {
        always {
            // 这里可以添加一些清理步骤，例如清理工作目录或通知
            sh 'docker system prune -f'
        }
        success {
            echo 'Build and deployment succeeded!'
        }
        failure {
            echo 'Build or deployment failed.'
        }
    }
}
