node {
    stage('Build') {
        def pythonImage = docker.image('python:2-alpine')
        pythonImage.inside {
            sh 'python -m py_compile /sources/add2vals.py /sources/calc.py'
        }
    }

    stage('Test') {
        def pytestImage = docker.image('qnib/pytest')
        pytestImage.inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
        step([$class: 'JUnitResultArchiver', testResults: 'test-reports/results.xml'])
    }

    stage('Deliver') {
        def pyinstallerImage = docker.image('cdrx/pyinstaller-linux:python2')
        pyinstallerImage.inside {
            sh 'pyinstaller --onefile sources/add2vals.py'
        }
        archiveArtifacts artifacts: 'dist/add2vals', allowEmptyArchive: true
    }
}
