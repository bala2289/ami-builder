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
                    rm -rf packer-templates
                    ssh -o StrictHostKeyChecking=no git@github.com || true
                    git clone git@github.com:bala2289/packer-templates.git || true
                    git diff --name-only $GIT_PREVIOUS_COMMIT $GIT_COMMIT
                    '''
                }
            }
        }
        stage('Modify AMI id in templates') {
            steps {
                sh '''
                export newamiid=`cat new-ami-id.txt`
                cd packer-templates
                export oldamiid=`cat current-ami-id.txt`
                grep ami templates/couchdb/couchdb.json
                for i in `find templates/  -name *.json`;do sed -i "s/$oldamiid/$newamiid/g" $i; done
                grep ami templates/couchdb/couchdb.json
                echo $newamiid >> current-ami-id.txt
                '''
            }
        }
    post {
        success {
            echo 'Cleaning up..'
            sh 'rm manifest.json'
        }
    }
}