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
                bat 'mvn package'
            }
        }

        stage('Deploiement Nexus') {
    steps {
        bat 'mvn deploy -DskipTests'
    }
}

        /*
        stage('Deploy Nexus') {
            steps {
                bat 'mvn deploy'
            }
        }
        */
    }

    post {

        success {
            echo 'Build réussi'
        }

        failure {
            echo 'Le build Jenkins a échoué'
        }
    }
}