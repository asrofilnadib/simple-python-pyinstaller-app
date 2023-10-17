node {
    docker.image('python:2-alpine').inside {
        stage('Build') {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
        }
        stage('Test') {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }

        post {
            always {
                junit 'test-reports/results.xml'
            }
        }

        stage('Deliver') {
            sh 'pyinstaller --onefile sources/add2vals.py'
        }

        post {
            success {
                archiveArtifacts 'dist/add2vals'
            }
        }

    }
}