pipeline {
    agent any

    environment {
        NAME_CONTAINER = "pcks-logistic-cargaschedule-core"
        NAME_IMAGE = "pcks-logistic-cargaschedule-img:1"
        ID_CONTAINER = null
        PORT_CONTAINER = "9179:9179"
    }

    stages {

        stage('Git Checkout Repositorio') {
            steps {
                git branch: 'develop',
                url: 'https://github.com/packsendme/pcks-logistic-cargaschedule-core.git'
            }
        }
        stage('Java Build') {
          steps {
                sh 'mvn clean install'
            }
        }

        stage("Docker Delopy - Check Container") {
            steps {
                script {
                    ID_CONTAINER = sh(script: "docker ps -a -f name=${NAME_CONTAINER} --format {{.ID}}", returnStdout: true).trim()
                    echo "Deploy PR #${ID_CONTAINER}"
                }
            }
        }

        stage("Docker Delopy  - Stop Container") {
           when {
               allOf {
                        expression { ID_CONTAINER != null }
                        expression { ID_CONTAINER != "" }
               }
           }
            steps {
                script {
                    sh "docker stop ${ID_CONTAINER}"
                    sh "docker rm ${ID_CONTAINER}"
                    sh "docker rmi ${NAME_IMAGE}"
                }
            }
        }
        stage("Docker Delopy  - BuildAndRun Image") {
            steps {
                script {
                    sh "docker build . -t ${NAME_IMAGE}"
                    sh "docker run --name ${NAME_CONTAINER} -d -p ${PORT_CONTAINER} ${NAME_IMAGE}"
                }
            }
        }

    }
}
