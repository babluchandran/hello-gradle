#!/usr/bin/env groovy

pipeline {
    agent {label "master"}
    options { buildDiscarder(logRotator(numToKeepStr: '10')) }
		environment {
			SONAR_FLAG = '-Dsonar.host.url=http://13.127.220.12:9000 -Dsonar.analysis.mode= -Dsonar.report.export.path=sonar-report.json'
			//NEXUS_FLAG = 'nexusPublisher nexusInstanceId: 'localNexus', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/sampledemo-1.0.jar']], mavenCoordinate: [artifactId: 'jenkins-war', groupId: 'techm.cadt.com', packaging: 'jar', version: '2.00']]]'
			}
	
	
	
    stages {
        stage('Approval') {
            steps {
                script {
                    input(id: "Deploy Gate", message: "Deploy ?", ok: 'Deploy')                          
		}
              }
          }
	    
	stage('Checkout SCM') {
            steps {
                script {
                    checkout scm                            
				}
            }
        }  
	    
	stage('Gradle Build') {
            steps {
                script {
                    sh "gradle build"                            
				}
            }
        } 
        
    }
	    
    
	
	

	    
}
