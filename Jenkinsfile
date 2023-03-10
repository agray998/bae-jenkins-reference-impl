pipeline {
    agent any
    stages {
        stage ('Build React') {
            steps {
                git url: 'https://github.com/agray998/simple-node-js-react-npm-app'
                
                sh "npm install"
                sh "npm test"
                sh "npm publish --registry http://deploy-serve:8081/repository/mynpm"
            }
        }
        stage ('Build Maven') {
            steps {

                git url: 'https://github.com/agray998/SpringBoot-Jenkins',
                    branch: 'main'

                withMaven {

                // Run the maven build
                sh "mvn clean deploy" // deploy also runs all phases prior to deploy

                } // withMaven will discover the generated Maven artifacts, JUnit Surefire & FailSafe & FindBugs & SpotBugs reports...
            }
        }
        stage ('deploy') {
            // ssh into server, pull from nexus, run spring app and react app
            steps {
                sh '''
                ssh prod-serve << EOF
                mkdir backend && cd $_
                wget --user jenkins --password ${nexus_pass} http://deploy-serve:8081/repository/maven-releases/com/qa/tdl/1.0.0/tdl-1.0.0.war
                java -jar tdl-1.0.0.war
                mkdir ../frontend && cd $_
                npm install --registry http://deploy-serve:8081/repository/mynpm my-app
                npm start my-app
                EOF
                '''
            }
        }
    }
    post {
        always {
            mail to: "you@example.com",
                 subject: "Jenkins build ${env.BUILD_NUMBER}",
                 body: "Build no. ${env.BUILD_NUMBER} finished with status: ${currentBuild.currentResult}."
        }
    }
}
