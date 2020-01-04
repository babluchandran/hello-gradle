#!/usr/bin/env groovy

pipeline {
    agent {label "master"}
    options { buildDiscarder(logRotator(numToKeepStr: '10')) }
		environment {
			SONAR_FLAG = '-Dsonar.host.url=http://13.231.194.154:9000/ -Dsonar.analysis.mode= -Dsonar.report.export.path=sonar-report.json'
			gradle = '/opt/gradle/gradle-3.4.1/bin'
			//NEXUS_FLAG = 'nexusPublisher nexusInstanceId: 'localNexus', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: 'target/sampledemo-1.0.jar']], mavenCoordinate: [artifactId: 'jenkins-war', groupId: 'techm.cadt.com', packaging: 'jar', version: '2.00']]]'
			}

	
    stages {
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
                    sh "${gradle}/gradle build -Dsonar.projectKey=app"
                    
				}
            }
        }
        
		
		stage('Sonar') {
            steps {
                script {
			def scannerHome = tool 'SONAR'
			withSonarQubeEnv('SonarQube') {
                    	sh "${scannerHome}/bin/sonar-scanner"
                }
                    //sh "${gradle}/gradle sonarqube ${SONAR_FLAG}"                            
				}
            }
        }

		stage('Cobertura') {
            steps {
                script {
                    sh "${gradle}/gradle cobertura"
		     step([$class: 'CoberturaPublisher', autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: '**/target/site/cobertura/coverage.xml', failUnhealthy: false, failUnstable: false, maxNumberOfBuilds: 0, onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false])
				}
            }
        }
		
		stage('Push to Nexus') {
            steps {
                script {
                    //sh "mvn deploy -DaltDeploymentRepository=thirdparty::default::http://nexus-ose-cicdtools.apps.techm.name/nexus/content/repositories/thirdparty"
		            nexusPublisher nexusInstanceId: 'localNexus', nexusRepositoryId: 'releases', packages: [[$class: 'MavenPackage', mavenAssetList: [[classifier: '', extension: '', filePath: '/home/ubuntu/SampleWebApp.war']], mavenCoordinate: [artifactId: 'sample-war', groupId: 'techm.nissan.com', packaging: 'war', version: '$BUILD_NUMBER']]]
				}
            }
        }
	    
          
		stage('Docker Build Image') {
            steps {
                script {
                    app = docker.build("techmid/mong")                            
				}
            }
        }
        
		stage('Docker Push Image') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com','dockerID') {
                    app.push("${env.BUILD_NUMBER}")                            
				    }
                }
            }
        }
        
        stage('Approval') {
            steps {
                script {
                    input "continue to deploy?"                            
				}
            }
        }
        
		stage('Docker Deploy Image') {
            steps {
                script {
                    ansiblePlaybook extras: '-e version=$BUILD_NUMBER',  installation: 'ansible', inventory: '/etc/ansible/hosts', playbook: '/home/ubuntu/nissan_genx.yml' 
				}
            }
        }
    }
    
    
    
    
    
    
    
    
    
    post {
        success {
             mail to: 'BC00434278@techmahindra.com',
             subject: "Success Pipeline: ${currentBuild.fullDisplayName}",
             body: "Pipelien ${env.BUILD_NUMBER} passed "
           } 
           
        failure {
             mail to: 'BC00434278@techmahindra.com',
             subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
             body: "Something is wrong with ${env.BUILD_URL}"
           } 
	}

}
