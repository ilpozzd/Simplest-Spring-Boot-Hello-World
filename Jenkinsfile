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
          	    sh 'docker build --build-arg WAR=$(find ./target -name *.war) -t $CR/$REPO:$BUILD_ID .'
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'GitHub-CR', passwordVariable: 'CRPassword', usernameVariable: 'CRUser')]) {
        	        sh 'docker login $CR -u $CRUser -p $CRPassword'
                    sh "docker push ${CR}/${REPO}:${BUILD_ID}"
                }
            }
        }
        stage('Get Kubernetes Credentials') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'kubeconfig'),
                                file(credentialsId: 'kube-ca', variable: 'ca'),
                                file(credentialsId: 'kube-client-crt', variable: 'crt'),
                                file(credentialsId: 'kube-client-key', variable: 'key')]) {
                    sh 'mkdir ~/.kube/pki/ || true'
                    sh 'cp $ca ~/.kube/pki/ca.crt'
                    sh 'cp $crt ~/.kube/pki/client.crt'
                    sh 'cp $key ~/.kube/pki/client.key'
                    sh 'cp $kubeconfig ~/.kube/config'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'GitHub-CR', passwordVariable: 'CRPassword', usernameVariable: 'CRUser')]) {
        	        sh 'helm upgrade --install sber-task ./helm -n sber-task --create-namespace --set imageCredentials.username=$CRUser --set imageCredentials.password=$CRPassword --set image.tag=$BUILD_ID'
                }
            }
        }
    }
}