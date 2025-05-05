@Library('my-shared-library') _

pipeline {
    agent any

    parameters {
        choice(name: 'action', choices: ['create', 'delete'], description: 'Choose create/Destroy')
        string(name: 'ImageName', description: 'Name of the Docker build', defaultValue: 'javapp')
        string(name: 'ImageTag', description: 'Tag of the Docker build', defaultValue: 'v1')
        string(name: 'DockerHubUser', description: 'DockerHub Username', defaultValue: 'karthik27k')
    }

    stages {

        stage('Git Checkout') {
            when { expression { params.action == 'create' } }
            steps {
                gitCheckout(
                    branch: "main",
                    url: "https://github.com/karthik-27k/Java_app_3.0_Main.git"
                )
            }
        }

        stage('Unit Test (Maven)') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    mvnTest()
                }
            }
        }

        stage('Integration Test (Maven)') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    mvnIntegrationTest()
                }
            }
        }

        stage('Static Code Analysis: SonarQube') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    def SonarQubecredentialsId = 'sonarqube-api'
                    staticCodeAnalysis(SonarQubecredentialsId)
                }
            }
        }

        stage('Quality Gate Check: SonarQube') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    def SonarQubecredentialsId = 'sonarqube-api'
                    QualityGateStatus(SonarQubecredentialsId)
                }
            }
        }

        stage('Maven Build') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    mvnBuild()
                }
            }
        }

        stage('Docker Image Build') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    dockerBuild(params.ImageName, params.ImageTag, params.DockerHubUser)
                }
            }
        }

        stage('Docker Image Scan: Trivy') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    dockerImageScan(params.ImageName, params.ImageTag, params.DockerHubUser)
                }
            }
        }

        stage('Docker Image Push: DockerHub') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    dockerImagePush(params.ImageName, params.ImageTag, params.DockerHubUser)
                }
            }
        }

        stage('Docker Image Cleanup: DockerHub') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    dockerImageCleanup(params.ImageName, params.ImageTag, params.DockerHubUser)
                }
            }
        }

        stage('Data to DB - Grafana02 (Parallel)') {
            when { expression { params.action == 'create'
