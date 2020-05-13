pipeline {
  agent any
  options {
      skipStagesAfterUnstable()
  }
  stages {
    stage('Build') {
      steps {
        sh 'mvn -B -DskipTests clean package'
      }
    }
    stage('Test') {
      steps {
        sh 'mvn test'
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
        }
      }
    }
    stage('Deliver') {
      steps {
        script {
          sshPublisher(
            continueOnError: false, failOnError: true,
            publishers: [
              sshPublisherDesc(
                configName: 'ali',
                transfers: [
                  sshTransfer(
                    execCommand: 'pwd',
                  ),
                  sshTransfer(
                    cleanRemote: false,
                    excludes: '',
                    execTimeout: 120000,
                    flatten: false,
                    makeEmptyDirs: false,
                    noDefaultExcludes: false,
                    patternSeparator: '[, ]+',
                    remoteDirectory: './simple-java',
                    remoteDirectorySDF: false,
                    removePrefix: '',
                    sourceFiles: 'Jenkinsfile'
                  )
                ],
                usePromotionTimestamp: false,
                useWorkspaceInPromotion: false,
                verbose: true
              )
            ]
          )
        }
      }
    }
    stage('Deploy') {
      steps {
        script {
          if(currentBuild.currentResult == "SUCCESS" || currentBuild.currentResult == "UNSTABLE") {
            sh 'echo "Build Succeeded."'
          }
        }
      }
    }
  }
  post {
    always {
      emailext (
        subject: '[JENKINS]: $JOB_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!',
        body: """<!DOCTYPE html>
<html>
<head>
<style type="text/css">
::-webkit-scrollbar{ display: none; }
</style>
  <meta charset="UTF-8">
  <title>${JOB_NAME}-第${BUILD_NUMBER}次构建日志</title>
</head>

<body leftmargin="8" marginwidth="0" topmargin="8" marginheight="4"
offset="0">
<div class="main" style="width:100%;max-width:600px;margin-top:auto;margin-bottom:auto;margin-right:auto;margin-left:auto;">
  <div class="card" style="width: 100%;border-radius: 10px;box-shadow: 0 0 8px 0 rgba(0, 0, 0, 0.25);">
    <div class="bottom" style="width: 100%;background: #fff;border-radius: 0 0 10px 10px;">
<table width="95%" cellpadding="0" cellspacing="0"
style="font-size: 0.75rem; font-family: Tahoma, Arial, Helvetica, sans-serif;line-height: 1.3rem;color: #b4b4b4;padding: 20px 0 30px 40px">
<tr>
  <td><br />
    <b><font color="#0B610B">构建信息</font></b>
    <hr size="2" width="100%" align="center" />
  </td>
</tr>
<tr>
  <td>
    <ul>
      <li>项目名称 ：${env.PROJECT_NAME}</li>
      <li>构建状态 ：$BUILD_STATUS</li>
      <li>构建编号 ：第${BUILD_NUMBER}次构建</li>
      <li>触发原因 ：${env.CAUSE}</li>
      <li>构建日志 ：<a href="${env.BUILD_URL}console">${env.BUILD_URL}console</a></li>
      <li>构建URL ：<a href="${env.BUILD_URL}">${env.BUILD_URL}</a></li>
      <li>工作目录 ：<a href="${env.PROJECT_URL}ws">${env.PROJECT_URL}ws</a></li>
      <li>项目URL ：<a href="${env.PROJECT_URL}">${env.PROJECT_URL}</a></li>
    </ul>
  </td>
</tr>

</table>
    </div>
  </div>
</div>
</body>
</html>
""",
        to: 'zhb896579388@163.com'
      )
    }
  }
}
