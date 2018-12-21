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
                sh 'packer validate create-ami.json'
                }
        }
        stage('packer build AMI') {
            steps {
                sh 'pwd'
                sh 'packer build create-ami.json'
                sh 'cat manifest.json | jq -r .builds[0].artifact_id |  cut -d':' -f2'
            }
        }
    
    }
    post { 
        always { 
            echo 'Cleaning up..'
            sh 'rm manifest.json'
        }
    }
}
