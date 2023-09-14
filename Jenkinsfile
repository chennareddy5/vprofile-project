def COLOR_MAP = [
    'SUCCESS': 'Good',
    'FAILURE': 'Danger',
]

pipeline {
    agent any

    tools {
        maven "MAVEN3"
    }

    environment {
        NEXUS_VERSION = "nexus3"
        NEXUS_PROTOCOL = "http"
        NEXUS_URL = "172.31.40.209"
        NEXUS_PORT = "8081"
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
                    nexusUrl: '65.0.21.197:8081',
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

        stage("sonarqubescan") {
            steps {
                withSonarQubeEnv('sonarserver') {
                    sh "mvn sonar:sonar"
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    // Add steps for quality gate if needed
                }
            }
        }

        stage("UploadArtifactUploader") {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '65.0.21.197:8081',
                    groupId: 'QA', 
                    version: "${ARTVERSION}-${env.BUILD_TIMESTAMP}", 
                    repository: 'vprofile-release',
                    credentialsId: 'nexuslogin',
                    artifacts: [
                        [artifactId: 'vproapp',
                            classifier: '',
                            file: 'target/vprofile-v1.war',
                            type: 'war']
                    ]
                )
            }
            post {
                always {
                    echo "Slack Notification"
                    slackSend(
                        channel: '#jenkinsci',
                        color: COLOR_MAP[currentBuild.currentResult],
                        message: "${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
                    )
                }
            }
        }
    }
}

