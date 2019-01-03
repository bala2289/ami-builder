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
                        export artifactid=`cat manifest.json | jq -r .builds[0].artifact_id |  cut -d\':\' -f2`
                        echo $artifactid >> new-ami-id.txt
                '''
            }
        }
        stage('Git Clone Packer templates') {
            steps {
                sshagent(['f404bdfc-6e3a-4cac-9ff4-866b8e1bbcf1']) {
                sh '''
                    ssh -o StrictHostKeyChecking=no git@github.com || true
                    git clone git@github.com:bala2289/packer-templates.git || true
                    '''
                }
            }
        }
        stage('Modify AMI id in templates') {
            steps {
                sh '''
                export new-ami-id=`cat new-ami-id.txt`
                cd packer-templates
                grep ami couchdb/couchdb.json
                export old-ami-id=`cat current-ami.txt`
                for i in `find .  -name *.json`;do sed -i 's/$old-ami-id/$new-ami-id/g' $i; done
                grep ami couchdb/couchdb.json
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