#!/usr/bin/env groovy
/**
  ***** Dminds Devops ToolChain *****
  Documentation: https://
  Status: Continouse Improvement 
https://groovy-lang.org/semantics.html
*/
import org.jenkinsci.plugins.pipeline.modeldefinition.Utils

node {
    checkout scm
    deleteDir()
}

def seperator60 = '\u2739' * 60
def seperator20 = '\u2739' * 20
def seperator30 = '\u2739' * 30

def runParallel = true
def buildStages
def docker_login = "docker_hub_passwd"

node() {
    stage('fetch repo') {
        echo "${seperator60}\n${seperator20} Checking out Source Repo \n${seperator60}"
        deleteDir()
        checkout scm
    
    }

    stage('Build App') {
        echo "${seperator60}\n${seperator20} Long build app \n${seperator60}"
        wrap([$class: 'AnsiColorBuildWrapper', 'colorMapName': 'xterm']){
            dir("./shopizer-2.9.0"){
                sh """
                   ./mvnw clean install
                   ls -lart ./sm-shop/target 
                """
            } 
        }
    }

    stage("Build Image") {
        input 'Ready to build image ?'
    }

    stage('publish docker') {
        withCredentials([usernamePassword(credentialsId: 'docker_hub_creds', passwordVariable: 'DOCKER_REGISTRY_PWD', usernameVariable: 'DOCKER_REGISTRY_USER')]) {
            // assumes Jib is configured to use the environment variables
            sh """
                docker build -t phelun/shopizer_app:v0."$BUILD_NUMBER" .
                docker login -u ${env.DOCKER_REGISTRY_USER} -p ${env.DOCKER_REGISTRY_PWD}
                docker push phelun/shopizer_app:v0."$BUILD_NUMBER
            """
        }
    }

    stage('Package Artifacts') {
        echo "${seperator60}\n${seperator20} Login to docker registry and push new images \n${seperator60}"
        wrap([$class: 'AnsiColorBuildWrapper', 'colorMapName': 'xterm']){
            dir("./shopizer-2.9.0/sm-shop"){
                sh """
                   whoami 
                   docker build -t phelun/shopizer_app:v0."$BUILD_NUMBER" .
                """
            } 
        }

        docker.withRegistry('https://hub.docker.com/', 'docker_hub_creds') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
}