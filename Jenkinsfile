node {
        docker.image('python:2-alpine').inside {
            stage('Build') { 
                    sh 'python -m py_compile sources/add2vals.py sources/calc.py' 
                    stash includes: 'sources/*.py', name: 'compiled-results' 	
            }
        }             
        docker.image('qnib/pytest:latest').inside {
            stage('Test') {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
                junit 'test-reports/results.xml'
            }
        }
        stage('Deploy') {
            docker.image('cdrx/pyinstaller-linux:python2').inside {
                sh 'docker run -v "$(pwd):/sources/" --entrypoint /bin/sh cdrx/pyinstaller-linux:python2 "pyinstaller --onefile sources/add2vals.py"'
                archiveArtifacts artifacts: '${env.BUILD_ID}/sources/dist/add2vals', followSymlinks: false
            }
        }
}

