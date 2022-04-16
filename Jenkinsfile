pipeline {
	agent {
		label 'master'
	}
	tools {
		maven 'maven4'
	}
	parameters {
		choice (
			choices: ['false', 'true'], 
			description: 'Run CI Job', 
			name: 'ci'
			)
		choice (
			choices: ['false', 'true'], 
			description: 'Run cd Job', 
			name: 'cd'
			)
			
//		 string (
//			name: 'version', 
//			defaultValue: '9.0.0', 
//			description: 'deploy specified version'
//			)
	}
	environment {
		pom = readMavenPom file: 'pom.xml'
		version = "${pom.version}"
		artifactId = "${pom.artifactId}"
  	}
	stages {
//		stage('git_checkout') {
//			steps {
//				git credentialsId: 'git', url: 'https://github.com/mohancggrl/hello-world-war.git'
//			}
//		}
		stage('sonar') {
		when {
                	expression {ci == 'true'}
            	}
			steps {
				sh 'mvn clean install  sonar:sonar -Dsonar.host.url=http://3.83.230.109:9000/  -Dsonar.login=525226d75595e2802bcc44f4ace096ad7d616bb4'
			}
		}
		stage('build & deploy') {
		when {
                	expression {ci == 'true'}
            	}
			steps {
				sh 'mvn clean deploy'
			}
		}
		stage('deploy') {
		when {
                	expression {cd == 'true'}
            	}
		agent {
			label 'ansible'
                }
			steps {
				checkout([
					$class: 'GitSCM', 
					branches: [[name: '*/master']], 
					extensions: [], 
					userRemoteConfigs: [
					[
						credentialsId: 'git', 
						url: 'https://github.com/mohancggrl/devops_deploy.git'
					]
					]
				])
				sh 'ansible-playbook -i inventory.yml deploy.yml -e version=$version -e artifactId=$artifactId'
			}
		}
	}
}
