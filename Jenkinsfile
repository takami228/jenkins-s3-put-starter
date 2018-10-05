pipeline {
    agent any

    post {
        success {
            mattermostSend color: "good", message: ":smile: [${env.JOB_NAME}](${env.BUILD_URL}) was succeeded."
        }
        failure {
            updateGitlabCommitStatus name: 'release', state: 'failed'
            mattermostSend color: "danger", message: ":dizzy_face: [${env.JOB_NAME}](${env.BUILD_URL}) was failed."
        }
    }

    options {
        gitLabConnection('GitLab')
        gitlabBuilds(builds: ['release'])
    }

    triggers {
        gitlab(
            triggerOnPush: true,
            branchFilterType: 'All',
            addNoteOnMergeRequest: true,
            addCiMessage: true
        )
    }

    stages {
        stage('release') {
            steps {
                script{
                    def releaseVersion = env.BRANCH_NAME.startsWith('release/') ? env.BRANCH_NAME.substring('release/'.length()) : null
                    if(releaseVersion){
                        //Add Release Version File
                        if(fileExists("./version.txt")) {
                            sh 'rm ./version.txt'
                        }
                        sh "echo $releaseVersion >> ./version.txt"
                        sh "cat ./version.txt"

                        //S3 Sync
                        s3Upload(file:'Manager', bucket:'s3-buckat-name', path:'path/to/folder') //To Do

                        //GitLab Status
                        updateGitlabCommitStatus name: 'release', state: 'success'

                        //Mattemost Send
                        mattermostSend color: "good", message: ":smile: ${releaseVersion} was released to S3"

                        //Tag Push & Delete Branch
                        def userRemoteConfig = scm.userRemoteConfigs.head()
                        withCredentials([usernameColonPassword(credentialsId: userRemoteConfig.credentialsId, variable: 'GIT_CREDENTIAL')]) {
                            def url = userRemoteConfig.url.replace('://', "://${env.GIT_CREDENTIAL}@")
                            sh 'git config user.email devops@example.com'
                            sh 'git config user.name devops'
                            sh "git tag -f $releaseVersion"
                            sh "git push $url $releaseVersion -f"
                            sh "git push $url --delete ${env.BRANCH_NAME}"
                        }
                    }
                }
            }
        }
    }
}
