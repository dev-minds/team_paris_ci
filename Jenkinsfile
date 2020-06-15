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

    // stage('Build App') {
    //     echo "${seperator60}\n${seperator20} Long build app \n${seperator60}"
    //     wrap([$class: 'AnsiColorBuildWrapper', 'colorMapName': 'xterm']){
    //         dir("./shopizer-2.9.0"){
    //             sh """
    //                ./mvnw clean install
    //             """
    //         } 
    //     }
    // }

    stage('Package Artifacts') {
        echo "${seperator60}\n${seperator20} Look up artifacts \n${seperator60}"
        wrap([$class: 'AnsiColorBuildWrapper', 'colorMapName': 'xterm']){
            dir("./shopizer-2.9.0/sm-shop/target"){
                sh """
                   ls -lart
                """
            } 
        }
    
    }

    stage("Build Image") {
        input 'Ready to build image ?'
    }

    stage('Package Artifacts') {
        echo "${seperator60}\n${seperator20} Login to docker registry and push new images \n${seperator60}"
        wrap([$class: 'AnsiColorBuildWrapper', 'colorMapName': 'xterm']){
            dir("./shopizer-2.9.0/sm-shop"){
                sh """
                   whoami 
                   docker login -u phelun -p "${docker_login}"
                   docker build -t phelun/shopizer_app:v0."$BUILD_NUMBER" .
                   docker push <your_username>/shopizer_app:v0."$BUILD_NUMBER"
                """
            } 
        }
    }
}