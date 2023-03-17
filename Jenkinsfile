pipeline { 
    // agent any
    // docker { image 'ubuntu:latest' }
    agent {
        docker { image 'maven:amazoncorretto' }  
     }
    tools {
        maven 'mvn-3.9.0'
    }
    stages {
        stage('Git-Pull') { 
            steps { 
                git 'https://github.com/PriyaSIVATH/sonarqube-maven-example.git'
            }
        }
        
        stage('Build') { 
            steps { 
                sh '''
                    mvn clean package
                '''
            }
        }
        
        stage('Archive Artifact') {
            steps {
                archiveArtifacts(artifacts: '**/*.jar', followSymlinks: false)
            }
         }

        stage('Artifact Upload to Nexus3') {
            steps {
                nexusArtifactUploader artifacts: [
                    [   artifactId: 'sonarqube-maven-example', 
                        classifier: '', 
                        file: 'target/sonarqube-maven-example-1.0-SNAPSHOT.jar', 
                        type: 'jar' ]
                        ],  credentialsId: 'nexus-repo-manager', 
                            groupId: 'com.sonarqube.example', 
                            nexusUrl: '192.168.0.155:8081', 
                            nexusVersion: 'nexus3', 
                            protocol: 'http', 
                            repository: 'my-maven-repo', 
                            version: '1.0-SNAPSHOT'
            }
         }
        
    }
}