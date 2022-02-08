node {																																												 
    withMaven(maven:'M3') {
        stage('Checkout') {
            def scmVars = checkout scm
	        env.IMAGE_TAG = scmVars.GIT_COMMIT[0..6] + '.' + env.BUILD_NUMBER
        }

        stage("Build") {
            withSonarQubeEnv('sonarqube-server') {
                sh 'mvn -s /var/jenkins_home/settings.xml -U clean org.jacoco:jacoco-maven-plugin:prepare-agent package sonar:sonar'
            }
	}
    }
}
																  
