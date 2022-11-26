pipeline {

    agent any
    
    stages {

        stage('Build') {
            steps {
                echo 'Build'
                sh "mvn --batch-mode package" 
            }
        }

        stage('Code Quality'){
            steps{
                scrips{
                   sh 'sonar-scanner \
                      -Dsonar.projectKey=Prueba \
                      -Dsonar.sources=. \
                      -Dsonar.host.url=http://172.17.0.5:9000 \
                      -Dsonar.login=squ_e788b56ade6945f7e4e7ad32c4c0f1ca368c4db0'
                }
            }
        }

        stage('Archive Unit Tests Results') {
            steps {
                echo 'Archive Unit Test Results'
               step([$class: 'JUnitResultArchiver', testResults: 'target/surefire-reports/TEST-*.xml'])
            }
        }
        
        stage('Publish Unit Test results report') {
            steps {
                echo 'Report'
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: true, keepAll: false, reportDir: 'target/site/jacoco/', reportFiles: 'index.html', reportName: 'jacaco report', reportTitles: ''])

             }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploy'
                sh '''
                    for runName in `docker ps | grep "alpine-petclinic" | awk '{print $1}'`
                    do
                        if [ "$runName" != "" ]
                        then
                            docker stop $runName
                        fi
                    done
                    docker build -t alpine-petclinic -f Dockerfile.deploy .
                    docker run --name alpine-petclinic --rm -d -p 9966:8080 alpine-petclinic 
                '''
            }
        }
    }
}
