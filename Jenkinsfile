#!/usr/bin/env groovy
pipeline {
    agent any
    stages {
        stage('pipeline start') {
            steps {
                sh 'echo "pipeline initiated.."'
            }
        }
        stage('packer validate template') {
            steps {
                sh 'chmod +x /usr/local/bin/packer'
                sh 'pwd'
                sh 'packer validate ubuntu16-base.json'
                }
        }
        stage('packer build AMI') {
            steps {
                sh 'packer build ubuntu16-base.json'
                sh '''
                        export artifactid=`cat manifest.json | jq -r .builds[0].artifact_id |  cut -d\':\' -f2
                        echo $artifactid
                '''
            }
        }
        stage('Git Clone Packer templates') {
            steps {
                sshagent(['f404bdfc-6e3a-4cac-9ff4-866b8e1bbcf1']) {
                sh '''
                    git@github.com:bala2289/packer-templates.git
                    cd packer-templates
                    ls
                '''
                }

            }
        }
    post { 
        success {
            echo 'Cleaning up..'
            sh 'rm manifest.json'
            }
        }
    }
}