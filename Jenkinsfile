pipeline{
    environment {
        Docker_image = "springbootapp"
        SONAR_URL = "http://192.168.1.130:9000" 
        
    }
    agent {
        docker { image '9902736822/maven_project_agent:latest'
        args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
    }
    }
    
    stages{
        stage('Git check out'){
            steps{
                script{
                    git branch: 'main', credentialsId: '28059df9-d0e4-49f6-9da6-e410f9470aff', url: 'https://github.com/pgr-automation/CICD_spring_boot.git'
                    
                }
            }
        }
        stage("build"){
            steps{
                sh '''
                cd spring-bootapp/
                export MAVEN_OPTS="--add-opens java.base/java.lang=ALL-UNNAMED"
                mvn clean package 
            
                '''
            }
        }
        stage('SonarQube Code Analysis'){
            steps{
                withCredentials([string(credentialsId: 'SonarQube', variable: 'SONAR_AUTH_TOKEN')]) {
                    sh '''
                        cd spring-bootapp/
                        mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}
                        '''
                }

            }
        }
        stage('Docker Image Build'){
             
            steps{
                sh '''
                    cd spring-bootapp/  
                    docker build -t ${Docker_image} .
                '''
            }
        }
        
    }
}