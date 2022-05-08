pipeline {
    agent any
    stages {
        stage('Git clone') {
            steps {
                sh 'echo "Hello world"'
                sh 'echo $WORKSPACE'
               // git 'https://github.com/ReBedc/helloworld_python_UNIR.git'
               script {
                 scmVars = checkout scm
                 echo 'scm : the commit id is ' + scmVars.GIT_COMMIT
               }
            }
        }
        stage('Build') {
            steps {
              echo 'El workspace contiene el commit \'' + scmVars.GIT_COMMIT + '\' de la rama \'' + scmVars.GIT_BRANCH + '\''
            }
        }
        stage('Tests'){
            parallel{
                stage('Unit tests') {
                    steps {
                        catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                            sh '''
                                export PYTHONPATH=$WORKSPACE
                                echo $PYTHONPATH
                                echo $PATH
                                export PATH=//Library//Frameworks//Python.framework//Versions//3.10//bin:$PATH
                                echo $PATH
                                python app//calc.py
                                pytest --junitxml=result-unit.xml test//unit
                            '''
                        }
                    }
                }
                stage('Service tests') {
                    steps {
                        catchError(buildResult: 'UNSTABLE', stageResult: 'FAILURE') {
                            sh '''
                                export PATH=/Library/Frameworks/Python.framework/Versions/3.10/lib/python3.10/site-packages:/Library/Frameworks/Python.framework/Versions/3.10/bin:/opt/homebrew/bin:/opt/homebrew/sbin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:$PATH
                                echo $PATH
                                export FLASK_APP=app//api.py
                                export FLASK_ENV=development
                                pip3 install requests
                                python3 -m venv development
                                . development//bin//activate
                                flask run & > logFlask.log
                                export PYTHONPATH=$WORKSPACE
                                pytest --junitxml=result-rest.xml test//rest
                            '''
                        }
                    }
                }
            }
        }

        stage('Results') {
            steps {
                junit 'result*.xml'
            }
        }
    }
}
