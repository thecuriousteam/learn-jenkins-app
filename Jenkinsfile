pipeline {
    agent any

    stages {
        stage('build') {
          agent{

            docker {
              image 'node:18-alpine'
              reuseNode true
            }
          }
            steps {
                echo 'Building the application'
                sh '''
                ls -la
                node --version
                npm --version
                npm ci
                npm run build
                echo 'Build completed'
                ls -la
                '''
            }
        }

        // test stage
        stage('test'){

          agent{
            docker{
              image 'node:18-alpine'
              reuseNode true
            }
          }
          steps{
            sh '''
            echo "Testing the application"
            ls -la
            test -f build/index.html
            npm test
            '''
          }
        }


         // test stage
        stage('end to end test'){

          agent{
            docker{
            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'              reuseNode true

              // this needs to run as administrator
            }
          }
          steps{
            sh '''
            echo "Performing end to end test"
            npm install serve
            node_modules/.bin/serve -s build &
            sleep 10
            npx playwright test
            '''
          }
        }
    }


  // display test results stage (publishing reports)
  post{
    // always -> always run irrespective of pipeline success or failure
    always{
        junit 'jest-results/junit.xml'
    }
  }
}
