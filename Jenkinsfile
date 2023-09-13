pipeline {
    agent any

    tools {
        maven "MAVEN3"
    }

    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "172.31.40.209:8081"
        NEXUS_REPOSITORY = "vprofile-release"
        NEXUS_REPOGRP_ID = "vprofile-grp-repo"
        NEXUS_CREDENTIAL_ID = "nexuslogin"
        SONARSERVER = "sonarserver"
        SONARSCANNER = "sonarscanner"
        ARTVERSION = "${env.BUILD_ID}"
    }

    stages {
        stage('checkout') {
            steps {
                git 'https://github.com/chennareddy5/vprofile-project.git'
            }
        }

        stage('BUILD') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage('UNIT TEST') {
            steps {
                sh 'mvn test'
            }
        }

        stage('package') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Upload Artifacts') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '3.110.164.211:8081',
                    groupId: 'com.visualpathit',
                    version: 'v2',
                    repository: 'vprofile-release',
                    credentialsId: 'nexuslogin',
                    artifacts: [
                        [artifactId: 'vprofile', type: 'war', file: 'target/vprofile-v1.war']
                    ]
                )
            }
        }

        stage('Checkstyle Analysis') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage("sonarqubescan"){
            steps{
            withSonarQubeEnv('sonarserver'){
            sh "mvn sonar:sonar"    
            }
        }
    }
        stage("Quality Gate"){
            steps{
                timeout(time: 1, unit: 'HOURS') {
                }
            }
        }stage("Publish to Nexus Repository") {
            steps {
                script {
                        nexusArtifactUploader(
                            nexusVersion: "${NEXUS_VERSION}",
                            protocol: "${NEXUS_PROTOCOL}",
                            nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                            groupId: "${NEXUS_GRP_REPO}",
                            version: "${ARTVERSION}",
                            repository: "${RELEASE_REPO}",
                            credentialsId: "${NEXUS_LOGIN}",
                            artifacts: [
                                [artifactId: 'vproapp',
                                classifier: '',
                                file: 'target/vprofile-v2.war',
                                type: 'war']
                            ]
                        )
                   }
            }
        }
                
    
        
    }
}
