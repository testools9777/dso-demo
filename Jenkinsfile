pipeline {
  agent {
    kubernetes {
      yamlFile 'build-agent.yaml'
      defaultContainer 'maven'
      idleMinutes 1
    }
  }
  stages {
    stage('Build') {
      parallel {
        stage('Generate BOM') {
          steps {
            container('maven') {
              sh 'mvn test'
            }
          }
        }
	stage('SCA') {
	steps {
	container('maven') {
	catchError(buildResult: 'SUCCESS', stageResult:
	'FAILURE') {
	sh 'mvn org.cyclonedx:cyclonedx-maven-plugin:makeAggregateBom'

	}
	}
	}

}
}      
      }
   
    stage('Static Analysis') {
      parallel {
        stage('Unit Tests') {
          steps {
            container('maven') {
              sh 'mvn test'
            }
          }
        }
      }
    }
    stage('Package') {
      parallel {
        stage('Create Jarfile') {
          steps {
            container('maven') {
              sh 'mvn package -DskipTests'
            }
          }
       
        }
 stage('Docker BnP') {
			steps {
				container('kaniko') {
				sh '/kaniko/executor -f `pwd`/Dockerfile -c `pwd` --insecure --skip-tls-verify --cache=true --destination=docker.io/testools9777/dsodemo'
        	}
        	}
            }
          }
         }

    stage('Deploy to Dev') {
      steps {
        // TODO
        sh "echo done"
      }
    }
  }
}

