# jenkins-s3-starter

JenkinsとGitLabとMattermostとAWS S3を連携するサンプルプロジェクトです。

PushトリガーでJenkinsジョブを実行し、`release/*` ブランチをpushした場合にのみ成果物をS3にPutします。

# Requirements

* Jenkins
* Jenkins Plugins
  * [GitLab Plugin](https://github.com/jenkinsci/gitlab-plugin)
  * [Pipeline AWS Plugin](https://github.com/jenkinsci/pipeline-aws-plugin)
  * [Mattermost Nortification Plugin(Optional)](https://github.com/jenkinsci/mattermost-plugin)
* GitLab
* Mattermost(Optional)
* AWS S3
