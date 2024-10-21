def registry = 'https://idkdevops.jfrog.io/'
def imageName = 'idkdevops.jfrog.io/idk-docker-local/idk'
def version   = '2.1.3'
pipeline {
    agent {
        node {
            label 'maven'
        }
    }
environment {
    PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
}
    stages {
        stage ("build"){
            steps {
                sh 'mvn clean deploy'
            }
        }
        stage("Jar Publish") {
            steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"jfrog-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "idk-libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'  
            
            }
        }   
    }

        stage(" Docker Build ") {
            steps {
        script {
           echo '<--------------- Docker Build Started --------------->'
           app = docker.build(imageName+":"+version)
           echo '<--------------- Docker Build Ends --------------->'
        }
      }
    }

        stage (" Docker Publish "){
            steps {
            script {
               echo '<--------------- Docker Publish Started --------------->'  
                docker.withRegistry(registry, 'jfrog-cred'){
                    app.push()
                }    
               echo '<--------------- Docker Publish Ended --------------->'  
            }
        }
    }

        stage(" Deploy ") {
            steps {
            script {
            echo '<--------------- Helm Deploy Started --------------->'
            sh 'sudo helm install ttrend ttrend1-0.1.0.tgz'
            echo '<--------------- Helm deploy Ends --------------->'
         }
       }
     }
//        stage('SonarQube analysis') {
//    environment {
//      scannerHome = tool 'sonarqube-scanner'
//   }
// steps {
//   withSonarQubeEnv('sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
//      sh "${scannerHome}/bin/sonar-scanner"
//    }
//  }
    }
}
