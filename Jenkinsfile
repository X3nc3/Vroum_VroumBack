pipeline {
    agent any

    tools {
        maven 'Maven3'
        jdk 'Java21'
    }

    environment {
        SONARQUBE_ENV = 'SonarQube 1'
        SONAR_PROJECT_KEY = 'Vroum_Vroum'
        SRV_DEPLOY = '172.29.86.140'
    }

    stages {
        stage('Vérification Git') {
            steps {
                sh 'git --version'
            }
        }

        stage('Clonage du dépôt') {
            steps {
                git 'https://github.com/X3nc3/Vroum_VroumBack.git'
            }
        }

        stage('Lister les fichiers') {
            steps {
                sh 'ls -la && cat README'
            }
        }

        stage('Tests unitaires') {
            steps {
                echo "======================> mvn test"
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/test-classes/demo/vroum_vroum/*'
                }
            }
        }

        stage('Analyse SonarQube') {
            steps {
                withSonarQubeEnv("${env.SONARQUBE_ENV}") {
                    sh "mvn sonar:sonar -Dsonar.projectKey=${env.SONAR_PROJECT_KEY}"
                }
            }
        }

        stage('Validation manuelle pour déploiement') {
            steps {
                input message: 'Souhaitez-vous déployer en staging ?'
            }
        }

        stage('deploiement') {
            steps {
                sh '''
                echo "Déploiement réussi vers $env.SRV_DEPLOY"
                scp target/*.jar user@${env.SRV_DEPLOY}:/opt/tomcat/webapps/
                '''
            }
        }
    }

    post {
        success {
            echo "La pipeline terminée avec succès"
        }
        failure {
            echo "La pipeline a échoué"
        }
    }
}
