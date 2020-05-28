pipeline {
  agent none
  options {
    skipStagesAfterUnstable()
  }
  stages {
    stage('Build') {
      agent {
        docker {
          image 'python:3-alpine'
        }
      }
      steps {
        sh 'python -m py_compile sources/add2vals.py sources/calc.py'
      }
    }
    stage('Test') {
      agent {
        docker {
          image 'qnib/pytest'
        }
      }
      steps {
        sh 'py.test --verbose --junit-xml test-reports/results.xml sources/test_calc.py'
      }
      post {
        always {
          junit 'test-reports/results.xml'
        }
      }
    }
    stage('Deliver') {
      agent {
        none
      }
      steps {
        sh 'docker run -v "$(pwd):/src" cdrx/pyinstaller-linux "pyinstaller --onefile sources/add2vals.py"'
      }
      post {
        success {
          archiveArtifacts 'dist/add2vals'
        }
      }
    }
  }
}
