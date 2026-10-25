pipeline {
    agent any

    tools {
        jdk 'JAVA_HOME'
        maven 'MAVEN_HOME'
    }

    stages {
        stage('Cloning spring petclinic repository') {
            steps {
                git branch: 'main', url: 'https://github.com/spring-petclinic/spring-framework-petclinic.git'
            }
        }

        stage('Compiling the spring pet clinic project') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Testing the spring pet clinic project') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Analyzing source code via SonarQube') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh 'mvn verify sonar:sonar -Dsonar.projectName=spring-petclinic -Dsonar.projectKey=spring-petclinic'
                }
            }
        }

        stage('Deploying build into nexus repository') {
            steps {
                withMaven(globalMavenSettingsConfig: 'nexus-config', jdk: 'JAVA_HOME', maven: 'MAVEN_HOME', traceability: true) {
                    sh 'mvn deploy'
                }
            }
        }

        stage('Deploy the build into tomcat server') {
            steps {
                sshagent(['tomcat-cred']) {
                    sh 'scp -o StrictHostKeyChecking=no target/petclinic.war ubuntu@13.208.242.131:/home/ubuntu/apache-tomcat-10.1.52/webapps'
                }
            }
        }
    }

    post {
        success {
            mail bcc: '',
                 body: 'war build is deployed successfully into tomcat server',
                 cc: '',
                 from: '',
                 replyTo: '',
                 subject: 'Build successful',
                 to: 'emailid@gmail.com' 
        }

        failure {
            mail bcc: '',
                 body: 'war build is not deployed successfully into tomcat server',
                 cc: '',
                 from: '',
                 replyTo: '',
                 subject: 'Build failure',
                 to: 'emailid@gmail.com'
        }
    }
}
