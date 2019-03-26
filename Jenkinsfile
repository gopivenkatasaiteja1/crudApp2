pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = "gopivenkatasaiteja1/crudapp"
        
    }
    tools {
     //   jdk 'Java'
        maven 'Maven2'
    }
        stages {
            stage('Build') {
                steps {
                    sh 'mvn -Dmaven.test.failure.ignore=true clean package'
                    //sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
                    }
            }
            stage ('Nexus') {
                steps {
                    nexusArtifactUploader artifacts: [[artifactId: 'crudApp', classifier: '', file: 'target/crudApp.war', type: 'war']], credentialsId: 'b8f15902-7ad9-4888-86b1-bafdd29c7ebf', groupId: 'Central', nexusUrl: '172.31.33.68:8081/nexus', nexusVersion: 'nexus2', protocol: 'http', repository: 'releases', version: '1.${BUILD_NUMBER}'
                }
            }
            stage ('Docker Build') {
                when {
                    branch 'master'
                } 
                steps {
                    sh 'wget http://172.31.33.68:8081/nexus/service/local/repositories/releases/content/central/crudApp/1.${BUILD_NUMBER}/crudApp-1.${BUILD_NUMBER}.war -O crudApp.war'
                    script {
                        app = docker.build(DOCKER_IMAGE_NAME)
                    }
                    sh 'rm -rf crud*'
                }
            }
           stage ('Docker Push Image') {
                when {
                    branch 'master'
                } 
                steps{
                    script {
                        docker.withRegistry('https://registry.hub.docker.com', 'docker_hub') {
                            app.push("${env.BUILD_NUMBER}")
                            app.push("latest")
                        }
                    }
                }
            }
            
            
            }
        }
}
