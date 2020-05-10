pipeline {
	agent any
	stages {
		stage ('Clone) {
			steps {
				echo 'Clone git repo'
				git 'https://github.com/RobinMeykens-PXL/2TIN_ResearchProject.git'
			}
		}

		stage ('Build') {
			steps {
				sh 'composer install'
			}
		}

		stage ('Test') {
			steps {
				sh './vendor/bin/phpunit tests'
			}
		}

		stage ('Artifact') {
			steps {
				archiveArtifacts artifacts: '/assets'
				archiveArtifacts artifacts: '/src'
				archiveArtifacts artifacts: '/tests'
				archiveArtifacts artifacts: 'composer.json'
				archiveArtifacts artifacts: 'employees.sql'
				archiveArtifacts artifacts: 'index.php'
			}
		}

		stage ('Apache') {
			steps {
				sh ''sudo cp -R /var/lib/jenkins/workspace/Pipeline/* /var/www/html/'
			}
		}

		stage ('Check-Git-Secrets') {
			steps {
				sh 'sudo rm trufflehog || true'
				sh 'sudo docker run gesellix/trufflehog --json https://github.com/cehkunal/webapp.git > trufflehog'
				sh 'sudo cat trufflehog'
			}
		}

		stage ('OWASP Dependency-Check') {
			steps {
				sh 'sudo rm owasp || true'
				sh 'sudo wget "https://raw.githubusercontent.com/cehkunal/webapp/master/owasp-dependency-check.sh"'
				sh 'sudo chmod +x owasp-dependency-check.sh'
				sh 'sudo bash owasp-dependency-check.sh'
				sh 'sudo cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
			}
		}

		stage ('SAST') {
			steps {
				withSonarQubeEnv {
					sh 'mvn sonar:sonar'
					sh 'cat target /sonar/report-task.txt'
				}
			}
		}
	}

	post {
		always {
			junit 'build/reports/**/*.xml'
		}
	}
}
