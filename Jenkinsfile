#!/usr/bin/env groovy

import groovy.json.JsonSlurperClassic

pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
		sh 'echo checkout'
		// git branch: 'master', credentialsId: 'cb39e95a-5ede-49c0-b51f-83624a733e40', url: 'git@bitbucket.org:lumsb/examples.git'    
                // checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'cb39e95a-5ede-49c0-b51f-83624a733e40', url: 'git@bitbucket.org:lumsb/examples.git']]])
            }
        }    
        stage('Clean') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            steps {
		sh 'mvn compile'
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
	/*
        stage('Static Analysis for Sonar') {
            steps {
                withSonarQubeEnv('YSBSonarqube') {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }
        stage('Sonar Gate') {
            steps {
                sh 'echo "sonar gate ok"'
            } 
        }
	*/
        stage('Build & Publish') {
            steps {
                sh 'mvn package'
		sh 'mvn install'
                sh 'echo "publish to artifactory ok"'
                hygieiaArtifactPublishStep artifactDirectory: 'target', artifactGroup: 'com.johannesburg.app', artifactName: 'my-johannesburgapp', artifactVersion: "1.0-SNAPSHOT"
            } 
        }
        stage('QA') {
            steps {
                sh 'echo "deploy to qa ok"'
            }
            post { 
                success {
                    hygieiaDeployPublishStep applicationName: 'my-johannesburg', artifactDirectory: 'target', artifactGroup: 'com.johannesburg.app', artifactName: 'my-johannesburgapp', artifactVersion: "1.0-SNAPSHOT", buildStatus: 'Success', environmentName: 'QA'
                }
                failure { 
                    hygieiaDeployPublishStep applicationName: 'my-johannesburg', artifactDirectory: 'target', artifactGroup: 'com.johannesburg.app', artifactName: 'my-johannesburgapp', artifactVersion: "1.0-SNAPSHOT", buildStatus: 'Failure', environmentName: 'QA'
                }
            }
        }
        stage('Stage') {
            steps {
                sh 'echo "deploy to stage ok"'
            }
            post { 
                success {
                    hygieiaDeployPublishStep applicationName: 'my-johannesburg', artifactDirectory: 'target', artifactGroup: 'com.johannesburg.app', artifactName: 'my-johannesburgapp', artifactVersion: "1.0-SNAPSHOT", buildStatus: 'Success', environmentName: 'Stage'
                }
                failure {
                    hygieiaDeployPublishStep applicationName: 'my-johannesburg', artifactDirectory: 'target', artifactGroup: 'com.johannesburg.app', artifactName: 'my-johannesburgapp', artifactVersion: "1.0-SNAPSHOT", buildStatus: 'Failure', environmentName: 'Stage'
                }
            }
        }
        stage('Prod') {
            steps {
                sh 'echo "deploy to production ok"'
            }
            post { 
                success {
                    hygieiaDeployPublishStep applicationName: 'my-johannesburg', artifactDirectory: 'target', artifactGroup: 'com.johannesburg.app', artifactName: 'my-johannesburgapp', artifactVersion: "1.0-SNAPSHOT", buildStatus: 'Success', environmentName: 'Prod'
                }
                failure {
                    hygieiaDeployPublishStep applicationName: 'my-johannesburg', artifactDirectory: 'target', artifactGroup: 'com.johannesburg.app', artifactName: 'my-johannesburgapp', artifactVersion: "1.0-SNAPSHOT", buildStatus: 'Failure', environmentName: 'Prod'
                }
            }
        }
        stage('Hygieia') {
            steps {
                sh 'echo "Update Hygieia"'
		// build 'HygieiaJenkinsPluginAlt'    
            }
	}
    }
}
