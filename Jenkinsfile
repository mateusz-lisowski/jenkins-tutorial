pipeline {
    agent {
        docker {
            image 'python:alpine'
            reuseNode true
        }
    }
    stages {
        stage('Hello') {
            steps {
                sh '''
                    echo 'print("Hello from python")' > main.py
                    python main.py
                '''
            }
        }
    }
}
