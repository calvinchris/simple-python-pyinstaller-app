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
        docker.image('cdrx/pyinstaller-linux:python2').inside('-v $(pwd)/sources:/src') {
            stage('Deliver') {
                dir('env.BUILD_ID') {
                    unstash 'compiled-result' 
                    sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'"
                }
                sh 'pyinstaller --onefile sources/add2vals.py'
                archiveArtifacts artifacts: 'dist/add2vals', followSymlinks: false
            }
        }
        
}
