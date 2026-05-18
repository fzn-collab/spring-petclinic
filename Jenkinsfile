pipeline {

    agent any

    tools {
        maven 'Maven'
        jdk 'JDK17'
    }

    stages {

        stage('Compilation') {
            steps {
                bat 'mvn clean compile'
            }
        }

        stage('Tests Unitaires') {
            steps {
                bat 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Couverture de Code') {
            steps {
                bat 'mvn jacoco:report'
            }
        }

        stage('Analyse Qualite') {
            parallel {
                stage('Checkstyle') {
                    steps {
                        bat 'mvn checkstyle:checkstyle'
                    }
                }
                stage('PMD') {
                    steps {
                        bat 'mvn pmd:pmd'
                    }
                }
            }
        }

        stage('Documentation') {
            steps {
                bat 'mvn site'
            }
        }

        stage('Packaging') {
            steps {
                bat 'mvn package -DskipTests'
            }
        }

        stage('Deploiement Nexus') {
            steps {
                bat 'mvn deploy -DskipTests -Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true'
            }
        }

        stage('Docker Build') {
    steps {
        bat 'docker build -t %DOCKER_USER%/petclinic:latest .'
    }
}
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                    bat 'docker push %DOCKER_USER%/petclinic:latest'
                }
            }
        }
    }

    post {
        failure {
            mail to: 'nokfatimazahra@gmail.com',
                 subject: "ECHEC Pipeline - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                 body: "Le build ${env.BUILD_NUMBER} a echoue. Voir : ${env.BUILD_URL}"
        }
        success {
            echo 'Build reussi !'
        }
    }
}