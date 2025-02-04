pipeline {
    agent any
    tools { 
        maven 'Maven 3.8.6'
    }
    stages {
        stage ('Initialize Tool') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                ''' 
            }
        }
        stage ('Build Backend') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }
        
        stage ('Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }
        
        /*
        stage ('Sonar Analysis') {
        	environment{
        		scannerHome = tool 'SONAR_SCANNER'
        	}
            steps {
            	withSonarQubeEnv('SONAR_LOCAL'){
                	sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000  -Dsonar.login=3aec06eeba4b617edb7623f693067a3ccc7fcfda -Dsonar.java.binaries=target"
               }
            }
        }

        stage ('Quality Gate') {
            steps {
          	    waitForQualityGate abortPipeline: true
            }
        }*/

        
        stage ('Deploy Backend'){
            steps {
                deploy adapters: [tomcat8(credentialsId: 'tomcat_login', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }
        stage ('API Test'){
            steps {
                dir('api-test') {
                    git branch: 'master', credentialsId: 'github_login', url: 'https://github.com/diegomacedocp/tasks-api-test'
                    sh 'mvn test'
                }
            }
        }
        stage ('Deploy Frontend'){
            steps {
                dir('tasks-frontend') {
                    git branch: 'master', credentialsId: 'github_login', url: 'https://github.com/diegomacedocp/tasks-frontend'
                    sh 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'tomcat_login', path: '', url: 'http://localhost:8001')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }

        /*stage ('Funcional Test'){
            steps {
                dir('funcional-test') {
                    git branch: 'master', credentialsId: 'github_login', url: 'https://github.com/diegomacedocp/tasks-funcional-tests'
                    bat 'mvn test'
                }
            }
        }*/

        stage ('Deploy Producao'){
            steps {
                /*dir('funcional-test') {*/
                dir('tasks-frontend') {
                    sh 'docker-compose build'
                    sh 'docker-compose up -d'
                }
            }
        }
/*

        stage ('HealthCheck'){
            steps {
                sleep(270)
                dir('funcional-test') {
                   bat 'mvn verify -Dskip.surefire.tests'
                }
            }
        }

        post{
            always{
                junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml, api-test/target/surefire-reports/*.xml, funcional-test/target/surefire-reports/*.xml, funcional-test/target/failsafe-reports/*.xml'
                archiveArtifacts artifacts: 'target/tasks-backend.war, frontend/target/tasks.war', onlyIfSuccessful: true
            }
        }
        unsuccessful{
            emailext attachLog: true, body: 'See the attached log below', subject: 'Build $BUILD_NUMBER has failed', to: 'rodrigofa1305+jenkins@gmail.com'
        }
        fixed{
            emailext attachLog: true, body: 'See the attached log below', subject: 'Build is Fine!', to: 'rodrigofa1305+jenkins@gmail.com'
        }
        */
    }
}
