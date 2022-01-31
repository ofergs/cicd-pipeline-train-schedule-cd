pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
            stage('DeployToStaging')  {
                steps   {
                    withCredentials([usernamePassword((credentialsId:'webserver_login',usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')])    {
                        sshPublisher(
                            failOnError: true,
                            continueOnError: false,
                            publishers: [
                                sshPublisherDesc(
                                    configName: 'staging',
                                    sshCredentials: [
                                        username: "$USERNAME",
                                        encryptedPassphrase: "$USERPASS"
                                    ],
                                    transfers: [
                                        sshTransfer(
                                            sourceFiles: 'dist/trainSchedule.zip',
                                            removePrefix: 'dist/',
                                            removeDirectory: '/tmp',
                                            execCommand: 'sudo /user/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule && sudo /usr/bin/systemctl start train-schedule'
                                        )
                                     ]
                                 )
                           ]
                      )
                 } 
           }
    }
}
