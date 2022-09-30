node {
        stage('Build') { 
           docker {
                    image 'python:2-alpine' 
                }
            sh 'python -m py_compile sources/add2vals.py sources/calc.py' 
            stash(name: 'compiled-results', includes: 'sources/*.py*') 
        }
}