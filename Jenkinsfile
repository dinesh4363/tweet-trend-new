pipeline {
    agent {
        node {
            label 'maven'
        }
    }
environments {
    PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
}
    stages {
        stage ("build"){
            steps {
                sh mvn clean deploy
            }
        }
    }
}

