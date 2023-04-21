pipeline {
    agent any

     stages {
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
                            set PYTHONPATH=$WORKSPACE
                            pytest --junitxml=result-unit.xml test\\unit
                        '''
                    }
                }
                stage('Service') {
                    steps {
                        sh '''
                            FLASK_APP="app\\api.py"
                            FLASK_ENV="development"
			    nohup Flask run
 			    nohup java -jar $WORKSPACE\wiremock\wiremock-jre8-standalone-2.28.0.jar --port 9090 --root-dir $WORKSPACE\wiremock
                            ping  -n 21 127.0.0.1 > /dev/null
                            PYTHONPATH=$WORKSPACE
			    pytest --junitxml=result-rest.xml test\rest			    
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
