pipeline {
   agent {
	  label "${params.slave_name}"
	}

   stages {
      stage('Clone') {
         steps {
      // Get some code from a GitHub repository
          checkout scm
         }
      }
      stage('Build') {
         steps {
		
            // Run Maven on a Unix agent.
		 sh "${params.build_cmd}"

            // To run Maven on a Windows agent, use
            // bat "mvn -Dmaven.test.failure.ignore=true clean package"
         }
      }
      stage('docker build') {
         steps {
		 sh "docker build -t=${params.docker_tag} ."
         }
      }
       stage('docker push') {
         steps {
             sh "docker push ${params.docker_tag}"
         }
      }
       stage('docker run') {
         steps {
             sh "docker stop \$(docker ps -a -q)"
			 sh "docker rm \$(docker ps -a -q)"
			 sh "docker run --name mynginx1 -p 80:80 -d ${params.docker_tag}"
         }
      }
        
   }
	  post {
    always {
	    script {
        def result = currentBuild.result
        if (result == null) {
          result = "SUCCESS"
        }
      }
     // echo 'always runs regardless of the completion status of the Pipeline run'
    }
    success {
      echo 'step will run only if the build is successful'
    }
    failure {
      echo 'only when the Pipeline is currently in a "failed" state run, usually expressed in the Web UI with the red indicator.'
    }
    unstable {
      echo 'current Pipeline has "unstable" state, usually by a failed test, code violations and other causes, in order to run. Usually represented in a web UI with a yellow indication.'
    }
    changed {
      echo 'can only be run if the current Pipeline is running at a different state than the previously completed Pipeline'
    }
  }
}
