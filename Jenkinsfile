@Library('sharedlib') _
import src.dataxu.docker.general.*

def gemname = 'delayed_job'
def gemspec = 'delayed_job.gemspec'
def region = 'us-east-1'

def docker_img = "${JOB_NAME}-builder".toLowerCase()
def general_docker = new dataxu.docker.general(this)
def ruby_utils = new dataxu.ruby.utils()


//Syntax quick guide: https://jenkins.io/doc/book/pipeline/syntax/
pipeline {
    agent {
        node {
            label 'micro'
            customWorkspace "/var/lib/jenkins/jobs/${JOB_NAME}/${BUILD_NUMBER}"
        }
    }
    options {
        //keep last 10 builds
        buildDiscarder(logRotator(numToKeepStr: '10'))
        //timestamps
        timestamps()
    }
    //begin stage definitions
    stages {
        stage ('Checkout SCM') {
            steps {
                //clean the current directory
                deleteDir()
                //get the repository this Jenkinsfile lives in
                checkout scm
            }
            post {
                always {
                    github_notify_status()
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    ruby_utils.write_push_script(
                        gemname: gemname,
                        region: region,
                        filepath: "${env.WORKSPACE}/push.sh"
                    )
                }
                sh """
                   chmod +x ${env.WORKSPACE}/push.sh
                   docker build -t ${docker_img} .
                   """
            }
            post {
                always {
                    github_notify_status()
                }       
            }
        }
        stage('Run Tests') {
            steps {
                sh """
                   docker run ${docker_img} bundler exec rake
                   """
            }
            post {
                always {
                    github_notify_status()
                }       
            }
        }
        stage('Build & Push Gem') {
            when {
                expression { env.BRANCH_NAME == 'master' }
            }
            steps {
                sh """
                   docker run ${docker_img} /bin/bash -c "gem build ${gemspec}; ./push.sh"
                   """
            }
            post {
                always {
                    github_notify_status()
                }       
            }
        }
    }
    post {
        always {
            script {
                general_docker.delete_image(docker_img)
                github_notify_status(stage_name: 'Pipeline complete')
            }
        }
    }
}
