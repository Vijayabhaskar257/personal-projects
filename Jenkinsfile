@Library('shared_library') _
pipeline {
    /*
    agent {
        node{
            label 'NBU'
            customWorkspace "workspace/${env.JOB_NAME}"
            }
    }
    */
    agent any	
    environment {
        git credentialsId: '211ef6be-f448-43ff-9c69-c90ef0e7addc', url: 'https://github.com/Vijayabhaskar257/personal-projects.git'   
    }
    options {
        buildDiscarder(logRotator(artifactDaysToKeepStr: '30', artifactNumToKeepStr: '5', daysToKeepStr: '30', numToKeepStr: '5'))
        timestamps()
    }
    tools {
        maven 'maven-3.8.5-auto'
        //jdk 'JDK-1.8-new'
    }
    stages {
        stage('Compile') {
            steps {
                sh "echo hi"
		sh "mvn install"
            }
        }
    
    
    stage('Sonar Scan placeholder'){
           steps {
             	sh "mvn sonar:sonar"	
		
           }
	 }	    

    stage('Build docker image'){
           steps {
             	sh "id"	
		sh "docker build -t nilart/personal-projects:${BUILD_NUMBER} ."
           }
	 }	    
    
    stage('Docker login and push') {
            steps {
              withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]) {
                sh "docker login -u nilart -p ${DockerHubPwd}"
		sh "docker push  nilart/personal-projects:${BUILD_NUMBER}"
              }
            
            }  
         }  
    stage('Depoly microservice via k8s yaml on k8s setup via ansible') {
            steps {
              withCredentials([string(credentialsId: 'DockerHubPwd', variable: 'DockerHubPwd')]) {
                sh "echo use_ansibleplaybook_to_deploy_simple_pod_servie_depolyment_yml"
		
              }
            
            }  
         }    
	    
    }
    post {
        always{
            cleanWorkspace()
	    print "hi"	
        }
        success {
            emailext attachLog: true,
                body: 'Pipeline job ${JOB_NAME} success. Build URL: ${BUILD_URL}',
                recipientProviders: [[$class: 'CulpritsRecipientProvider']],
                subject: 'SUCCESS: Jenkins Job- ${JOB_NAME} Build No- ${BUILD_NUMBER}',
                to: 'nilesh.arte@calsoftinc.com'
        }
        failure {
            emailext attachLog: true,
                body: 'Pipeline job ${JOB_NAME} failed. Build URL: ${BUILD_URL}',
                recipientProviders: [[$class: 'CulpritsRecipientProvider'], [$class: 'DevelopersRecipientProvider'], [$class: 'FailingTestSuspectsRecipientProvider'], [$class: 'UpstreamComitterRecipientProvider']],
                subject: 'FAILED: Jenkins Job- ${JOB_NAME} Build No- ${BUILD_NUMBER}',
                to: 'nilesh.arte@calsoftinc.com'
        }
    }
}
