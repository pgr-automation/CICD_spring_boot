pipeline{
    environment {
        Docker_image = "springbootapp:${Build_Version}"
        SONAR_URL = "http://192.168.1.130:9000" 
        DOCKER_REGISTRY = "https://hub.docker.com/"
        DOCKER_CREDENTIALS_ID = "1001"
        
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
        stage('Image scan using trivy'){
            
            steps {
                script{
                    def scanResult = sh(script: "trivy image --exit-code 1 --severity HIGH,CRITICAL ${Docker_image}", returnStatus: true)
                    if (scanResult != 0){
                        error "Image scanning failed. High or Critical vulnerabilities found."
                    }
                    else {
                        echo "Image Passed Vulnerabilities Scan "
                    }
                }
               
            
            }
            
        }

        stage('Tag Image and Push to registry'){
            steps{
                withDockerRegistry(credentialsId: '1001', url: 'https://registry-1.docker.io/v2/') {
                    script{
                        sh '''
                            echo "Docker tag Image"
                            docker tag ${Docker_image} 9902736822/${Docker_image}
                            docker push 9902736822/${Docker_image}
                        '''

                    }
                }

            }
        }
        
        stage('Deleting Old Version Image'){
            agent none
            steps{
                sh '''
                docker rmi -f 9902736822/springbootapp:${Del_Version}
                docker rmi -f springbootapp:${Del_Version}
                docker rmi -f springbootapp:${Build_Version}
                '''
            }
        }    
        
    }
}