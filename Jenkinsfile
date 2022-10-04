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
                
                docker.image('cdrx/pyinstaller-linux:python2') {
                withEnv(['VOLUME = \'$(pwd)/sources:/src\'', 'IMAGE = \'cdrx/pyinstaller-linux:python2\'']) {
                    sh 'docker run --rm -v ${VOLUME} ${IMAGE} \'pyinstaller --onefile add2vals.py\''
                    archiveArtifacts artifacts: 'sources/dist/add2vals', followSymlinks: false
                }
            }
        }
}

