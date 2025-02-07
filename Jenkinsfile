node {
    stage('CheckSCM') {
        checkout scm
    }
    checkout scm
    stage('Build') {
        //docker.image('python:2-alpine').inside {
        docker.image('python:3.9').inside {
            sh 'python -m py_compile sources/add2vals.py sources/calc.py'
            stash(name: 'compiled-results', includes: 'sources/*.py*')
        }
    }
    stage('Test') {
        docker.image('qnib/pytest').inside {
            sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
        }
        junit 'test-reports/results.xml'
    }
    stage('Manual-Approval') {
        input message: 'Lanjutkan ke tahap Deploy?'
    }
    stage('Deploy') {
        //docker.image('cdrx/pyinstaller-linux:python2').inside {
        docker.image('python:3.9').inside('--user root') {
            sh 'pip install pyinstaller'
            sh 'pyinstaller --onefile sources/add2vals.py'
            sleep time: 1, unit: 'MINUTES'
        }
        archiveArtifacts 'dist/add2vals'
    }
}