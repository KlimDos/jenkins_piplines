/*
Stop start ec2 instance AWS
*/

def ec2_id1_state
def ec2_id2_state
def ec2_id3_state
def ec2_id4_state

// testing
//def ec2_id1_state1 = '"stopped"'
//def ec2_id2_state1 = '"stopped"'
//def ec2_id3_state1 = '"running"'
//def ec2_id4_state1 = '"stopped"'

pipeline {
    agent any

    environment {
        ec2_id1 = 'i-0f69178286a1f8bce'
        ec2_id2 = 'i-09d5fe7e7d9e1ae17'
        ec2_id3 = 'i-01853dad4fc41b05a'
        ec2_id4 = 'i-0e1a6802a82abe47f'
        // testing
        //ec2_id1 = 'i-03d105471ab97fa7b'
        //ec2_id2 = 'i-07445572f6c8fef65'
        //ec2_id3 = 'i-0c8ed9415b7877096'
        //ec2_id4 = 'i-0d30d2a4ca26e8481'

    }
    stages {
        stage('Cheking instances state') {
            steps {
                script{
                    ec2_id1_state = sh(
                        script: 'aws ec2 describe-instances --instance-ids $ec2_id1 | /usr/local/bin/jq ".Reservations[].Instances[].State.Name"',
                        returnStdout: true
                    ).trim()
                    ec2_id2_state = sh(
                        script: 'aws ec2 describe-instances --instance-ids $ec2_id2 | /usr/local/bin/jq ".Reservations[].Instances[].State.Name"',
                        returnStdout: true
                    ).trim()
                    ec2_id3_state = sh(
                        script: 'aws ec2 describe-instances --instance-ids $ec2_id3 | /usr/local/bin/jq ".Reservations[].Instances[].State.Name"',
                        returnStdout: true
                    ).trim()
                    ec2_id4_state = sh(
                        script: 'aws ec2 describe-instances --instance-ids $ec2_id4 | /usr/local/bin/jq ".Reservations[].Instances[].State.Name"',
                        returnStdout: true
                    ).trim()
                }
                script {
                    proceed = input message: "Would you like to proceed?\nCurrent cluster state:\nec2_id1_state = ${ec2_id1_state}\nec2_id2_state = ${ec2_id2_state}\nec2_id3_state = ${ec2_id3_state}\nec2_id4_state = ${ec2_id4_state}\n",
                    parameters: []
                }
            }
        }

        stage('Starting cluster'){
            when {
                expression { ec2_id1_state == '"stopped"' && ec2_id2_state == '"stopped"' && ec2_id3_state == '"stopped"' && ec2_id4_state == '"stopped"'}
            }
            steps {
                echo 'Starting cluster'
                sh 'aws ec2 start-instances --instance-ids $ec2_id1 $ec2_id2 $ec2_id3 $ec2_id4'
                sh 'aws ec2 wait instance-status-ok --instance-ids $ec2_id1 $ec2_id2 $ec2_id3 $ec2_id4'
                echo 'Cluster has started'
            }
        }
        stage('Stopping cluster'){
            when {
                expression { ec2_id1_state == '"running"' && ec2_id2_state == '"running"' && ec2_id3_state == '"running"' && ec2_id4_state == '"running"'}
            }
            steps {
                echo 'Stopping cluster'
                sh 'aws ec2 stop-instances --instance-ids $ec2_id1 $ec2_id2 $ec2_id3 $ec2_id4'
                sh 'aws ec2 wait instance-stopped --instance-ids $ec2_id1 $ec2_id2 $ec2_id3 $ec2_id4'
                echo 'Cluster has stopped'
            }
        }
        stage('Cluster is not fully starterd/stopped'){
            when {
                anyOf{
                    expression { ec2_id1_state !=  ec2_id2_state}
                    expression { ec2_id2_state !=  ec2_id3_state}
                    expression { ec2_id3_state !=  ec2_id4_state}
                }
            }
        
            steps {
                echo "Cluster is  not fully starterd/stopped"
                echo "Current insrtances state:"
                echo "ec2_id1_state = ${ec2_id1_state}"
                echo "ec2_id2_state = ${ec2_id2_state}"
                echo "ec2_id3_state = ${ec2_id3_state}"
                echo "ec2_id4_state = ${ec2_id4_state}"
            }
        }
    }
}
