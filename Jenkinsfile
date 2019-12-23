#!/groovy
pipeline {
    agent{
        docker{
            image 'maven:3.6.3'
            args '-v /root/.m2:/root/.m2'
    }
    environment {
        REPOSITORY = "https://github.com/ZhiTingXin/demo.git"
        SERVICE_DIR = "demo"
    }
    stages {
        stage('pull code') {
            steps {
                echo "start fetch code from git:${REPOSITORY}"
                deleteDir()
                git "${REPOSITORY}"
                script {
                    time = sh(returnStdout: true, script: 'date "+%Y%m%d%H%M"').trim()
                    git_version = sh(returnStdout: true, script: 'git log -1 --pretty=format:"%h"').trim()
                    build_tag = time + git_version
                }
            }
        }

        stage('build maven') {
            steps {
                echo "begin pkg"
                dir(SERVICE_DIR){
                    sh "ls -l"
                    sh "mvn -v"
                    sh "mvn -U -am clean package"
                }
            }
        }

        stage('build docker') {
            steps {
                echo "begin build image with docker"
                dir(SERVICE_DIR){
                    sh "ls -l"
                    sh "docker build -t ${SERVICE_DIR}:${build_tag} ."
                }
            }
        }
        stage('deploy'){
            steps {
                echo "run container"
                sh "docker images"
                sh "docker kill hello"
                sh "docker run --name hello -d -v /opt/jar/springBootDocker/logs:/log -p 8088:8080 ${SERVICE_DIR}:${build_tag}"
            }
        }
    }
}
