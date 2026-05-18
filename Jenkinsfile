pipeline {

    agent any

    tools {
        maven 'Maven'
        jdk 'JDK17'
    }

    stages {

        stage('SCM') {
            steps {
                git 'https://github.com/fzn-collab/spring-petclinic.git'
            }
        }

        stage('Compilation') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Tests Unitaires') {
            steps {
                sh 'mvn test'
            }

            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Couverture de Code') {
            steps {
                sh 'mvn jacoco:report'
            }
        }

        stage('Analyse Qualite') {
            parallel {

                stage('Checkstyle') {
                    steps {
                        sh 'mvn checkstyle:checkstyle'
                    }
                }

                stage('PMD') {
                    steps {
                        sh 'mvn pmd:pmd'
                    }
                }
            }
        }

        stage('Documentation') {
            steps {
                sh 'mvn site'
            }
        }

        stage('Packaging') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Deploy Nexus') {
            steps {
                sh 'mvn deploy'
            }
        }
    }

    post {

        success {
            echo 'Build réussi'
        }

        failure {
            mail to: 'nokfatimazahra@gmail.com',
            subject: 'ECHEC PIPELINE JENKINS',
            body: 'Le build Jenkins a échoué'
        }
    }
}