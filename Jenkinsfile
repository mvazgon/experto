pipeline {
    agent any

     stages {
	     
	stage('Clean Workspace'){
		steps{
			//limpiamos los directorios de trabajo, que se descarge la info
			sh '''
				rm -fr $WORKSPACE/*
				ls -lrth $WORKSPACE/
			'''
		}
	     }
        stage('Get Code') {
            steps {
                // Obtener código del repo
                //git 'https://github.com/anieto-unir/helloworld.git'
		script {
			scmVars = checkout scm
			echo 'scm : the commit id is ' + scmVars.GIT_COMMIT
		}
            }
        }
        
        stage('Build') {
            steps {
                echo 'Eyyy, esto es Python. No hay que compilar nada!!!'
        		echo 'El workspace contiene el commit \'' + scmVars.GIT_COMMIT + '\' de la rama \'' + scmVars.GIT_BRANCH + '\''
            }
        }
        
        stage('Tests') {
            parallel {
                stage('Unit') {
                    steps {
                        sh '''
                            PYTHONPATH=$WORKSPACE
			    echo $PYTHONPATH
			    python3 -m pytest --junitxml=result-unit.xml test/unit
                        '''
                    }
                }
                stage('Service') {
                    steps {
                        sh '''
			    PYTHONPATH=$WORKSPACE
                            FLASK_APP="app/api.py"
                            FLASK_ENV="development"
			    python3 -m flask --app app/api.py run &
 			    java -jar $WORKSPACE/wiremock/wiremock-jre8-standalone-2.35.0.jar --port 9090 --root-dir $WORKSPACE/wiremock & 
			    sleep 15
			    python3 -m pytest --junitxml=result-rest.xml test/rest
                        '''
                    }    
                }
            }
        }
        stage ('Results') {
            steps {
                junit 'result*.xml'
            }
        }
    }
}
