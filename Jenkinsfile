pipeline{
    agent any
    // escenarios -> escenario -> pasos
    environment{
        NPM_CONFIG_CACHE= "${WORKSPACE}/.npm"
        dockerImagePrefix = "us-west1-docker.pkg.dev/lab-agibiz/docker-repository"
        registry = "https://us-west1-docker.pkg.dev"
        registryCredentials = "gcp-registry"
    }

    stages{
        stage("Saludo al profesor"){ 
            steps{
                sh 'echo "Comenzando la tarea 4..."'
            }
        }
        stage ("Instalación de dependencias, testing y build") {
            agent {
                docker {
                    image 'node:22'
                    reuseNode true
                }
            }
            stages {
                stage("Instalación de dependencias"){
                    steps {
                        sh 'npm ci'
                    }
                }
                stage("Ejecución de pruebas"){
                    steps {
                        sh 'npm run test:cov'
                    }
                }
                stage("Construcción de la aplicación"){
                    steps {
                        sh 'npm run build'
                    }
                }
            }
        }
        stage ("Build y push de imagen docker"){
            steps {
                script {
                    docker.withRegistry("${registry}", registryCredentials ){
                        sh "docker build -t backend-nest-test-nlj ."
                        sh "docker tag backend-nest-test-nlj ${dockerImagePrefix}/backend-nest-test-nlj"
                        sh "docker tag backend-nest-test-nlj ${dockerImagePrefix}/backend-nest-test-nlj:${BUILD_NUMBER}"
                        sh "docker push ${dockerImagePrefix}/backend-nest-test-nlj"
                        sh "docker push ${dockerImagePrefix}/backend-nest-test-nlj:${BUILD_NUMBER}"
                    }
                }
            }
        }/*
        stage ("Actualización de Kubernetes"){
            agent{
                docker{
                    image 'alpine/k8s:1.30.2'
                    reuseNode true
                }
            }
            steps{
                withKubeConfig([credentialsId: 'gcp-kubeconfig']){
                    sh "kubectl -n lab-nlj set image deployments/backend-nest-nlj backend-nest-nlj=${dockerImagePrefix}/backend-nest-nlj"
                }
            }
        }*/
    }
}