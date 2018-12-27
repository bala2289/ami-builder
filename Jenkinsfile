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
                sh 'artifactid=`cat manifest.json | jq -r .builds[0].artifact_id |  cut -d\':\' -f2`'
                sh 'echo $artifactid'
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
