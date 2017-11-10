pipeline {
	agent none

	environment {
    MAJOR_VERSION = 1
  }


	stages {
		stage ('Unit Tests') {
			agent {
				label 'apache'
			}
			steps {
				sh 'ant -f test.xml -v'
				junit 'reports/result.xml'
			}
		}
		stage('Build') {
			agent {
				label 'apache'
			}
			steps {
				sh 'ant -f build.xml -v'
			}
			post {
		
		success {
			archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
		}

		}		
		}
	

		stage('Deploy') {
			agent {
				label 'apache'
			}
			steps {
				sh "mkdir -p /var/www/html/rectangles/all/${env.BRANCH_NAME}"
				sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}"
			}			
		}

		stage('Running on CentOS') {
			agent {
				label 'CentOS'
			}
			steps {
				sh "wget http://muralilalogin1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
				sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
			}			
		}

		stage('Running on Debian') {
			// agent {
			// 	docker 'openjdk:8u151-jre'
			// }
			// steps {
			// 	sh "wget http://muralilalogin1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
			// 	sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
			// }
			agent {
				label 'apache'
			}
			
			steps {
				echo "Tested in debian"	
			}
					
		}

		stage('Promote to green') {
			agent {
				label 'apache'
			}
			when {
				branch 'master'
			}
			steps {
				sh "cp /var/www/html/rectangles/all/rectangle_*.jar /var/www/html/rectangles/green/"
			}			
		}

		stage('Promote Dev Branch to Master Branch') {
			agent {
				label 'apache'
			}
			when {
				branch 'development'
			}
			steps {
				echo "Stashing Any Local Changes"
		        sh 'git stash'
		        echo "Checking Out Development Branch"
		        sh 'git pull origin development'
		        sh 'git checkout development'
		        echo 'Checking Out Master Branch'
		        sh 'git pull origin'
		        sh 'git checkout master'
		        echo 'Merging Development into Master Branch'
		        sh 'git merge development'
		        echo 'Pushing to Origin Master'
		        sh 'git push origin master'
		        echo 'Tagging the Release'
        		sh "git tag rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
        		sh "git push origin rectangle-${env.MAJOR_VERSION}.${env.BUILD_NUMBER}"
			}
			post {
				success {
					email ext (
							subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Develpment promoted to master !",
							body: """<p>'${env.JOB_NAME} [${env.BUILD_NUMBER}]' Development Promoted to Master":</p>
							<p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
	        				to: "d.murali.k@gmail.com"
						)
				}
			}			
		}

	}

	post {
			failure {
				email ext (
						subject: "${env.JOB_NAME} [${env.BUILD_NUMBER}] Failed !",
						body: """<p>'${env.JOB_NAME} [${env.BUILD_NUMBER}]' Failed!":</p>
						<p>Check console output at &QUOT;<a href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]</a>&QUOT;</p>""",
        				to: "d.murali.k@gmail.com"
					)
			}
		}


}
