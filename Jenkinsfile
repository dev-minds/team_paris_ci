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

node() {
    stage('fetch repo') {
        echo "${seperator60}\n${seperator20} Checking out Source Repo \n${seperator60}"
        deleteDir()
        checkout scm
    
    }

    stage('Build App') {
        echo "${seperator60}\n${seperator20} View Artifacts \n${seperator60}"
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
        echo "${seperator60}\n${seperator20} Check Point \n${seperator60}"
        input 'Ready to build image ?'
    }

    stage('Push Image') {
        echo "${seperator60}\n${seperator20} Build Login And Push Image\n${seperator60}"
        withCredentials([usernamePassword(credentialsId: 'docker_hub_creds', 
            passwordVariable: 'DOCKER_REGISTRY_PWD', 
            usernameVariable: 'DOCKER_REGISTRY_USER')]) {
            wrap([$class: 'AnsiColorBuildWrapper', 'colorMapName': 'xterm']){
                dir("./shopizer-2.9.0/sm-shop"){
                    sh """
                        docker build -t phelun/shopizer_app:v0."$BUILD_NUMBER" .
                        docker login -u ${env.DOCKER_REGISTRY_USER} -p ${env.DOCKER_REGISTRY_PWD}
                        docker push phelun/shopizer_app:v0."$BUILD_NUMBER"
                    """
                } 
            }
        }
    }
}