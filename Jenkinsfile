node {
        stage('Build') { 
            docker.image('python:2-alpine').inside {
                    sh 'python -m py_compile sources/add2vals.py sources/calc.py' 
                    stash includes: 'sources/*.py', name: 'compiled-results' 	
            }
        }             
        stage('Test') {
            docker.image('qnib/pytest:latest').inside {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
                junit 'test-reports/results.xml'
            }
        }
        stage('Deploy') {
            docker.image('cdrx/pyinstaller-linux:python2').inside {
                sh 'docker run -v "$(pwd)/sources:/src/" cdrx/pyinstaller-linux:python2 "pyinstaller --onefile sources/add2vals.py"'
            }
        }
}

