pipeline {
	agent none



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
				sh "mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}"
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
			agent {
				docker 'openjdk:8u151-jre'
			}
			steps {
				sh "wget http://muralilalogin1.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
				sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
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
				sh "cp /var/www/html/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
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
				echo "Stashing local changes"
				sh "git stash"
				echo "Checkout Dev Branch"
				sh 'git checkout development'
				echo 'Checkout out master branch'
				sh 'git checkout master'
				echo "Merging branches"
				sh 'git merge development'
				echo "Pushing to origin master"
				sh 'git push origin master'
			}			
		}

	}


}
