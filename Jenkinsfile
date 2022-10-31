pipeline {
    agent any
    tools {
        maven 'Main'
    }
    environment {
        SCM = 'https://github.com'
        CR = 'ghcr.io'
        REPO = 'ilpozzd/simplest-spring-boot-hello-world'
    }
    stages{
        stage('Build Maven') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ilpozzd/simplest-spring-boot-hello-world']]])
                sh 'mvn clean install'
            }
        }
        stage('Docker Build') {
            steps {
          	    sh 'docker build --build-arg WAR=$(find ./target -name *.war) -t ${CR}/${REPO}:${BUILD_ID} .'
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'GitHub-CR', passwordVariable: 'CRPassword', usernameVariable: 'CRUser')]) {
        	        sh "echo $CRpassword | docker login ${CR} -u ${CRUser} --password-stdin"
                    sh 'docker push ${CR}/${REPO}:${BUILD_ID}'
                }
            }
        }
    }
}