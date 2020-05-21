pipeline {
     agent any
     stages {
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
         stage('Upload to AWS') {
             steps {
               withAWS(region:'us-west-2',credentials:'aws-static') {
               s3Upload(file:'index.html', bucket:'ms-udap3-bucket-pub', path:'index.html')
               }
             }
         }
        node {
  def stageName
    
  try {
      stage ('Check ms-udap3-bucket-pub health') {
        stageName = 'ms-udap3-bucket-pub is down!'
        sh "curl -m 5 http://ms-udap3-bucket-pub.s3-website-us-west-2.amazonaws.com/"
      }
  } catch (e) {
    currentBuild.result = 'FAILURE'
    snsPublish(topicArn: 'arn:aws:sns:us-west-2:086340503613:aws-static-jenkins_BuildFailure', subject: 'ms-udap3-bucket-pub is down', message: 'Unable to reach the public bucket ms-udap3-bucket-pub is down - build failed')
  }
}
     }
}
