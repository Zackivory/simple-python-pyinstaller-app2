pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    stages {
        stage('Setup Environment') {
            steps {
                sh '''
                    python -m venv venv
                    . venv/bin/activate
                    pip install pytest
                '''
            }
        }
        stage('Build') {
            steps {
                sh 'python -m py_compile sources/add2vals.py sources/calc.py'
                stash(name: 'compiled-results', includes: 'sources/*.py*')
            }
        }
        stage('Test') {
            steps {
                sh '''
            . venv/bin/activate
            pytest --junit-xml test-reports/results.xml sources/test_calc.py
        '''
            }
            post {
                always {
                    junit 'test-reports/results.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                sh "pyinstaller --onefile sources/add2vals.py"
            }
            post {
                success {
                    archiveArtifacts 'dist/add2vals'
                }
            }
        }
    }
}