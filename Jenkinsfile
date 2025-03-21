pipeline {
  agent any

  stages {
    stage('Code Checkout') {
				steps {
					script {
					if (env.BRANCH_NAME == 'master'){
					checkout(
						[
							$class: 'GitSCM',
							branches: [[name: '*/master']],
							extensions: [
								[	$class: "PreBuildMerge",
									options: [
										mergeTarget: "dev",
										fastForwardMode: "FF",
										mergeRemote: "origin",
										mergeStrategy: "default"
									],
								],
								[
									$class: 'userIdentity',
									email: "vatshitesh1996@gmail.com",
									name: "Hitesh Sharma"
								],
							],
							userRemoteConfigs: [[url: 'https://github.com/hitesh211/SZ-devops_ci_cd.git']]						
						]
						)
					} else if (env.BRANCH_NAME == 'dev'){	
						checkout([
							$class: 'GitSCM', 
							branches: [[name: '*/dev']], 
							userRemoteConfigs: [[url: 'https://github.com/hitesh211/SZ-devops_ci_cd.git']]
							])
						}
					}
				}
		}
	stage('Deploying on Docker') {
		steps {
			script {
				if (env.BRANCH_NAME == 'master'){
				sh """
				sudo rm -rf /root/git_job_master
				sudo mkdir /root/git_job_master
				sudo cp -rvf . /root/git_job_master

				if sudo docker ps|grep master
				then
				sudo docker container stop master
				sudo docker rm -f master
				sudo docker run -dit -p 83:80 -v /root/git_job_master:/usr/local/apache2/htdocs --name master httpd
				else
				sudo docker run -dit -p 83:80 -v /root/git_job_master:/usr/local/apache2/htdocs --name master httpd
				fi
                """
			} else if (env.BRANCH_NAME == 'dev'){
				sh """
                sudo rm -rf /root/git_job
				sudo mkdir /root/git_job
				sudo cp -rvf . /root/git_job
				if sudo docker ps|grep dev
				then
				sudo docker container stop dev
				sudo docker rm -f dev
				sudo docker run -dit -p 82:80 -v /root/git_job:/usr/local/apache2/htdocs --name dev httpd
				else
				sudo docker run -dit -p 82:80 -v /root/git_job:/usr/local/apache2/htdocs --name dev httpd
				fi
                """
				} 
			}
			}
		}
		stage('Testing Build') {
		steps {
			script {
				if (env.BRANCH_NAME == 'master'){
				sh """
				if [ `curl -o /dev/null -s -w "%{http_code}\n" http://3.109.212.132:83/index.html` = 200 ];
				then
				echo "Merge Successful"
				else
				echo "Merge Failed"
				fi
                """
			} else if (env.BRANCH_NAME == 'dev'){
				sh """
				if [ `curl -o /dev/null -s -w "%{http_code}\n" http://3.109.212.132:82/index.html` = 200 ];
				then
				echo "Merge Successful"
				else
				echo "Merge Failed"
				fi
                """
				}
			}
			}
		}
	}
	
	post {
		success {
			script {
			if (env.BRANCH_NAME == 'master'){
				withCredentials([usernamePassword(credentialsId: 'dcc67488-d003-49ef-96f5-f86cc224ec5d', passwordVariable: 'Bholi@1234567', usernameVariable: 'hitesh211')]) {
				sh('git push https://${hitesh211}:${Bholi@1234567}@git@github.com:hitesh211/SZ-devops_ci_cd.git HEAD:master')
					}
				} else if (env.BRANCH_NAME == 'dev'){
				build wait: false, job: '../git_job_pipeline/master'
				}
			}
		}	
	}
}
