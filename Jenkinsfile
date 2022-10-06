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
        stage('Manual Approval') {
            input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed'
        }
        stage('Deploy') {      
                unstash 'compiled-results'    
                sh 'docker run --rm -v "$(pwd)/sources:/src" cdrx/pyinstaller-linux "pyinstaller -F /src/add2vals.py"'
                archiveArtifacts 'sources/dist/add2vals'
                sh 'docker run --rm -v "$(pwd)/sources:/src" cdrx/pyinstaller-linux "rm -rf /src/build /src/dist"'
                sh 'docker run -d --rm --name heroku-node -it --mount "type=bind,source=$(pwd),destination=/home" node:latest'
                sh 'docker container start heroku-node'
                sh 'docker exec -it heroku-node /bin/bash'
                sh 'npm install -g heroku'
                sh 'cd /home'
                sh 'heroku login -i'
                sleep time: 1, unit: 'MINUTES'
        }
}

