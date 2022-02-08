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
        
        
        stage ('k8s Deploy') {
		    wrap([$class: 'KubectlBuildWrapper', caCertificate: '', credentialsId: 'mcrew-kubedev', serverUrl: 'https://kubernetes:6443']) {
		    	sh "sed -i 's/:latest/:${IMAGE_TAG}/g' k8s/deployment.yaml"
	    	    sh "kubectl apply -f k8s/"
	
	    		try {
			  		timeout(time: 5, unit: 'MINUTES') {
	    			    sh "kubectl rollout status deployment/helloworld-deployment"
	    			}
	    		} catch(err) {
	    			sh "kubectl rollout undo deployment/helloworld-deployment"
	    			throw err
	    		}
			}
		}
    }
}
																  
