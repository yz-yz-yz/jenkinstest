#!groovy

import java.text.SimpleDateFormat

node(){
    def dateFormat = new SimpleDateFormat("yyyyMMddHHmm")
    def dockerTag = dateFormat.format(new Date())
    def dockerName='marco-test'

    stage('get souce code') {
        try {
            echo "get source code"
            checkout scm
        }
        catch(err) {
            echo "get source code failed"
            throw err
        }
    }

    stage('npm run build') {
        try{
            docker.image('node:12-alpine').inside {
                sh 'node --version'
                sh 'npm --version'
                sh "npm --registry https://registry.npm.taobao.org install"
                sh 'npm install'
                sh 'npm run build'
            }
            }
        catch(err){
                echo "npm run build failed"
                throw err
            }
    }

    stage('deploy with nginx') {
        try {
            sh 'pwd'
            sh 'ls'
            sh 'cp -r dist ./devops_build'

            sh "docker rm -f ${dockerName}"
            sh "docker build --no-cache=true -t ${dockerName}:${dockerTag} ./devops_build"

            sh "docker run -u root --name ${dockerName} -p 3000:80 -it -d ${dockerName}:${dockerTag}"

            // only retain last 3 images
            sh """docker rmi -f \$(docker images | grep ${dockerName} | sed -n  '4,\$p' | awk '{print \$3}') || true"""
        }
        catch(err){
                echo "deploy with Nginx failed"
                throw err
            }
    }
}
