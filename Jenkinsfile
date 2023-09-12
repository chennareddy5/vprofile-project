pipeline {
    
	agent any
/*	
	tools {
        maven "maven3"
    }
*/	
    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "172.31.40.209:8081"
        NEXUS_REPOSITORY = "vprofile-release"
	NEXUS_REPOGRP_ID    = "vprofile-grp-repo"
        NEXUS_CREDENTIAL_ID = "nexuslogin"
        ARTVERSION = "${env.BUILD_ID}"
    }
	
    stages{
        stage('checkout'){
            steps {
                git 'https://github.com/chennareddy5/vprofile-project.git'
            }
        }
        
        stage('BUILD'){
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

	stage('UNIT TEST'){
            steps {
                sh 'mvn test'
            }
        }
<<<<<<< HEAD
    	stage('package'){
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Upload Artifacts') {
          steps {
                    nexusArtifactUploader(
                      nexusVersion: 'nexus3',
                      protocol: 'http',
                      nexusUrl: '15.206.209.238:8081',
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
=======

>>>>>>> 0c2934aa2b010feca9a6a747cf975f8a3d4a58fd



    }


}
 
