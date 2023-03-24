pipeline { 
    // agent any
    // docker { image 'ubuntu:latest' }
    // agent {
    //     docker { image 'maven:amazoncorretto' }  
    //  }
    agent {
        docker {
            image 'maven:amazoncorretto'
            label 'docker-vm'
            // label 'docker-agent1'
        }
    }
    environment {
        tagName = "build.${BUILD_NUMBER}"
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

        stage('SonarQube Quality Check') {
            steps {
                withSonarQubeEnv('sonarqube1') {
                     sh '''
                        mvn sonar:sonar
                     '''
                }
            }
        }

        stage("Quality-Gate Status") {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                 waitForQualityGate abortPipeline: true
                }
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
                        file: 'target/sonarqube-maven-example-1.0-SNAPSHOT${tagName}.jar', 
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