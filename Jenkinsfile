#!/usr/bin/env groovy

def slave_label = rs_lib.slaveLabel

pipeline {
//    agent { label slave_label }
    agent { label 'hardware-windows' }

    environment {
         DOCKERFILE_DIR = "."
         SLACK_CHANNEL = "@jenkins"
    }

    options {
        gitLabConnection(rs_gitlab.connection)
        gitlabCommitStatus(name: rs_gitlab.buildUser)
        disableConcurrentBuilds()
//        ansiColor('xterm')
    }

    triggers {
        gitlab(triggerOnPush: true, triggerOnMergeRequest: false, branchFilterType: 'All')
    }

    stages {

       stage('Test') {

            steps {
                script {
                    try {

                        gitlabCommitStatus() {

                            parallel (

                                bullshit01: {

/*
                                    bat"""
                                        SET BRANCH=${env.BRANCH_NAME}
                                        SET
                                    """
*/

                                    sh"""
                                        echo "---------------------------"
                                        echo "JENKINSFILE TEST01"
                                        echo "---------------------------"
                                        uname -a
                                        echo "---------------------------"
                                        #env | sort
                                        echo "BRANCH_NAME: ${env.BRANCH_NAME}"
                                        echo "---------------------------"
                                        exit 0
                                    """
                                },
/*
                                bullshit02: {

                                    timeout(time: 20, unit: 'SECONDS') {
                                        build job: 'jenkinsfiletest02',
                                        propagate: false,
                                        wait: true,
                                        parameters: [
                                            string(name: 'SLEEP_TIME', value: "0"),
                                            string(name: 'EXIT_VALUE', value: "0")
                                        ]
                                    }
*/
                                    println("env.JOB_NAME: $env.JOB_NAME")

                                }
                            )
                        }
                    } catch (e) {
                        rs_cpp.slack_pipeline_exception(e)
                        throw e
                    }
                }
            }
        }
    }



    post {
        success { script { rs_web.slack_back_to_normal(currentBuild) } }
        //success { script { rs_web.slack_success(currentBuild) } }
        unstable { script { rs_web.slack_unstable() } }
        failure { script { rs_web.slack_failure() } }
    }
}
