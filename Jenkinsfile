pipeline {
    agent any
    //environment {
    //Use Pipeline Utility Steps plugin to read information from pom.xml into env variables
    // IMAGE = readMavenPom().getArtifactId()
    // VERSION = readMavenPom().getVersion()
    //}

    stages {
        stage('Sonar Analysis') {
            steps {
                echo 'Testing..'
                sh 'cd webapp && sudo docker run  --rm -e SONAR_HOST_URL="http://54.193.1.57:9000" -e SONAR_LOGIN="sqp_91192862a481df37852db8efa12f86958deab031"  -v ".:/usr/src" sonarsource/sonar-scanner-cli -Dsonar.projectKey=lms'
            }
        }

        stage('Build LMS app') {
            steps {
                echo 'Building Artifacts..'
                sh 'cd webapp && npm install && npm run build'
            }
        }

        stage('Release LMS') {
            steps {
                script {
                def packageJSON = readJSON file: 'webapp/package.json'
                def packageJSONVersion = packageJSON.version
                echo "${packageJSONVersion}"
                echo 'Store Artifacts....'
                // sh "echo '${packageJSONVersion}'"
                sh 'sudo rm -rf webapp/*.zip'
                sh "zip webapp/dist-'${packageJSONVersion}'.zip -r webapp/dist"
                sh "curl -v -u admin:Admin123* --upload-file webapp/dist-'${packageJSONVersion}'.zip http://13.92.5.110:8081/repository/lms/"             
                   //def data = readFile(file: 'webapp/package.json')
                   //println(data)
                //sh 'cd webapp && zip dist-${packageJSONVersion}.zip -r dist'
                //sh 'cd webapp && curl -v -u admin:Admin123* --upload-file dist-${packageJSONVersion}.zip http://13.92.5.110:8081/repository/lms/'
               }    
            }
        }

        stage('Deploy LMS') {
            steps {
                echo 'Deploying....'
                script {
                def packageJSON = readJSON file: 'webapp/package.json'
                def packageJSONVersion = packageJSON.version
                echo "${packageJSONVersion}"
                //sh 'cd webapp && zip dist-${packageJSONVersion}.zip -r dist'
                //sh 'cd webapp && curl -v -u admin:Admin123* --upload-file dist-${packageJSONVersion}.zip http://13.92.5.110:8081/repository/lms/'
                sh 'sudo rm -rf /var/www/html/*'
                sh 'curl -u admin:Admin123* -X GET \'http://13.92.5.110:8081/repository/lms/dist-${packageJSONVersion}.zip\' --output dist-'${packageJSONVersion}'.zip'
                sh 'sudo rm -rf dist'
                sh "sudo unzip -o dist-'${packageJSONVersion}'.zip"
                sh "sudo cp -r webapp/dist/* /var/www/html/"
               }                    
            }
        }
    }
}