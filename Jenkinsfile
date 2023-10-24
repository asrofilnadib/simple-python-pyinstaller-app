node {
    try {
        stage('Build') {
            def image = docker.image('python:3.12.0-alpine3.18')
            image.inside {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }

        stage('Test') {
            def testImage = docker.image('qnib/pytest')
            testImage.inside {
                sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py'
            }
            junit 'test-reports/results.xml'
        }

        stage('Manual Approval') {
            input message: 'Lanjutkan ke tahap Deploy? (Klik "Proceed" untuk melanjutkan)'
        }

        stage('Deliver') {
            def volume = '$(pwd)/sources:/src'
            def image = docker.image('cdrx/pyinstaller-linux:python2')
            image.inside("-v ${volume}") {
                dir("${env.BUILD_ID}") {
                    unstash(name: 'compiled-results')
                    sh "pyinstaller -F add2vals.py"
                }
            }

            archiveArtifacts artifacts: "${env.BUILD_ID}/sources/dist/add2vals", allowEmptyArchive: true

            def cleanupImage = docker.image('cdrx/pyinstaller-linux:python2')
            cleanupImage.inside("-v ${volume}") {
                sh "rm -rf build dist"
            }

            sleep time: 1, unit: 'MINUTE'
        }
    } catch (Exception e) {
        currentBuild.result = 'FAILURE'
        throw e
    } finally {
        junit '**/test-reports/*.xml'
    }
}
