pipeline {
    agent { label 'jenkins_node' }
    
    environment {
		DOCKERHUB_CREDENTIALS=credentials('dockerhubcred')
        sshagent=credentials('deploymentserver')
    }
    stages {
        stage('Creating Build') { 
            steps {
                echo 'Delete old jar file'
		sh"sudo rm -rf spring/ && ls"
                sh '''cd build/libs
                sudo rm -rf spring-boot-with-prometheus-0.1.0.jar
                ls
                cd ../..
                ./gradlew build
                echo "Checking if file is created"
                ls build/libs'''
            }
        }
        stage('Docker File Building') {
            steps {
                echo 'Removing old jar file from docker directory'
                sh "sudo rm -rf DOCKER/spring-boot-with-prometheus-0.1.0.jar"
                echo "Copy new build file to Docker directory"
                sh "sudo cp build/libs/spring-boot-with-prometheus-0.1.0.jar DOCKER/"
                sh '''cd DOCKER/
                sudo docker build -t dextercrypt/spring:${BUILD_NUMBER} .
                echo $DOCKERHUB_CREDENTIALS_PSW |sudo docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                sudo docker push dextercrypt/spring:${BUILD_NUMBER}'''
            }
        }
       stage('Deploying Spring App using Docker') {
            steps {
                sshagent(credentials:['deploymentserver']) {
                sh''' ssh -o StrictHostKeyChecking=no ubuntu@ec2-52-71-109-107.compute-1.amazonaws.com "sudo docker pull dextercrypt/spring:${BUILD_NUMBER} && sudo docker rm -f spring_prom_app && sudo docker run --restart=always --name spring_prom_app -d -p 8080:8080 dextercrypt/spring:${BUILD_NUMBER} && sudo docker ps && cat deployconfirm && ls && pwd"
                '''
            }
        }
    }
  }
}
