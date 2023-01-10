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
                sh "mvn clean verify"
                sh "mvn clean install"
                sh "mvn clean deploy"

                } // withMaven will discover the generated Maven artifacts, JUnit Surefire & FailSafe & FindBugs & SpotBugs reports...
            }
        }
        stage ('deploy') {
            // ssh into server, pull from nexus, run spring app and react app
        }
    }
}
