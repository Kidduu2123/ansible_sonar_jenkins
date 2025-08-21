pipeline {
    agent any

    tools {
        maven 'Maven3'  
    }

    environment {
        SONARQUBE_SERVER = 'SonarQube'          
        SONAR_TOKEN = credentials('sonarqube-token')  
    }

    stages {
        stage('Clone Code') {
           steps {
        git branch: 'main',
            url: 'https://github.com/Kidduu2123/ansible_sonar_jenkins.git',
            credentialsId: 'ac5160de-5167-469e-a728-be9388f00311'
    }
        }

        stage('Build WAR with Maven') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('SonarQube Code Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    sh "mvn sonar:sonar -Dsonar.login=${SONAR_TOKEN}"
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 15, unit: 'MINUTES') {
                    #waitForQualityGate abortPipeline: true
                }
            }
        }

        stage('Deploy using Ansible') {
            steps {
                sh '''
                    cd ansible_sonar_jenkins
                    ansible-playbook -i inventory.ini playbook.yml
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
