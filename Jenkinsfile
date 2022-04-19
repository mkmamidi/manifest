pipeline {
    agent any
    tools {
        git 'Default'
        jdk 'JAVA_HOME'
        maven 'M3'
    }
    stages {
        stage('SCM') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/Branch_Name']], extensions: [], userRemoteConfigs: [[credentialsId: 'creditials_id', url: 'bitbucket_clone_url']]])
            }
        }
        stage ("Build") { 
            steps{ 
                sh "mvn -X clean install -DskipTests=true -Dbuild.number=${env.BUILD_NUMBER} -Dproject.branchname=${env.GIT_BRANCH} -Dproject.builddate=${env.BUILD_TIMESTAMP}"
            }
            post {
                always {
                    emailext attachLog: true, body: '''
<p style="font-family:Serif; font-size: 12px"><span style="background-color: #FFFF00">The content of this email is confidential and intended for Company internal team usage only. It is strictly forbidden to share any part of this message with any third party.</span></p>
<br>
<h4 style="font-family:Serif; font-size: 15px"><b>Hai, Please find the JOB_name latest build status below</b></h4>
                    
<p style="font-family:Serif; font-size: 15px">Job Name : JOB_name - Build Number : # $BUILD_NUMBER - Build status : $BUILD_STATUS:</p><br><br>
                    
<i style="font-family:Serif; font-size: 15px">Please find the recent code changes below. </i><br>
                    
-------------------------------------------------------------------------------<br>
                    
<h5 style="color:green;"><b>${CHANGES}</b></h5><br>
-------------------------------------------------------------------------------   <br> 

<p><i style="color:black; font-family:Serif; font-size: 14px">If you need to check the build log information. Kindly check the attached log file. </i></p><br><br>
                    
<p style="font-family:Serif; font-size: 14px">Thanks</p>
<p style="font-family:Serif; font-size: 15px">DevOps Team</p><br>

            
<p style="color:red; font-family:Serif; font-size: 12px">** Note: This is an Auto genarated mail. We request you not to reply to this message. **</p>''',
                    mimeType: 'text/html',
                    subject: 'JOB_name - Build # $BUILD_NUMBER - $BUILD_STATUS!', 
                    to: 'email_id'
                }
            }
        }
        stage('Approval') {
            steps {
                emailext body: '''
<p style="font-family:Serif; font-size: 14px">Hai, </p>
                 
<p style="font-family:Serif; font-size: 14px">Latest CF_DEV_JAVA8 code build number is $BUILD_NUMBER, and build status is $BUILD_STATUS! </p>
                 

<p style="font-family:Serif; font-size: 14px"><i>Kindly click below Link to approve or deny the deployment request:</i></p> <br>
                 
<a href="${BUILD_URL}input" style="background-color: #f44336; color: white; padding: 15px 25px; text-align: center; text-decoration: none; display: inline-block; font-family:Serif; font-size: 14px">Click here to approve or deny</a>
<br>
<br>
<p style="font-family:Serif; font-size: 14px">Thanks</p>

            
<p style="color:red; font-family:Serif; font-size: 12px">** Note: This is an Auto genarated mail. We request you not to reply to this message. **</p>
''',
                subject: 'JOB_name - Staging deployment approval request',
                mimeType: 'text/html',
                to: "email_id"
            }
        }
        stage('Deploy') {
            input {
                    message '[Jenkins] ${jobName} Deploy Approval Request'
                    ok 'Deploy'
            }
            steps {
                sshagent(['credentials_Id']) {
                    sh """
                    scp -o StrictHostKeyChecking=no target/*.war user_name@ip_address:/tomcat/webapps
                    
                    ssh user_name@ip_address /tomcat/bin/shutdown.sh
                    
                    ssh user_name@ip_address /tomcat/bin/startup.sh
                    """
                }
            }
            post {
                aborted {
                    echo "Staging Deployment is aborted"
                }
            }
        }
      }
    }
}
