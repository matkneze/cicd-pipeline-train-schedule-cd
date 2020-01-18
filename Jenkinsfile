pipeline 
{
    agent any
    stages 
	{
        stage('Build') 
		{
            steps 
			{
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }
		stage('DeployToStagingServer') 
		{
			when 
			{
				branch 'master' 
			}
			

			steps 
			{
				withCredentials([usernamePassword(credentialsId: 'webserver_login', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) 
				{
					sshPublisher
					(
						publishers: 
						[
							sshPublisherDesc
							(
								configName: 'staging',
								sshCredentials 
								[	
									username: '$USERNAME',
									encryptedPassphrase: '$PASSWORD'
								],
								transfers: 
								[
									sshTransfer
									(
										execCommand: 'sudo systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip trainSchedule.zip && sudo systemctl start train-schedule',  
										remoteDirectory: '/opt/train-schedule', 
										sourceFiles: 'dist/trainSchedule.zip',
										removePrefix: 'dist/'
									)
								]
								
							)
						]
					)
				}				
			}	
		}
    }
}