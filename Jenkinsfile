pipeline {

    agent any
    environment {
        param1="clean compile"
    }

    stages {

        stage('Pre-Build') {

            steps {

                sh 'echo "Starting the script"'

                cleanWs()

                git branch: 'main', url: 'https://github.com/mrluthra/HelloWorldMaven.git'

                sh "echo '${param1}'"

                sh '''

                    echo '{

                     "token": "MyToken",

                     "createBuildSessionId": true,

                     "appName": "${env.JOB_NAME}",

                     "branchName": "feature/sealight",

                     "buildName": "${env.BUILD_NUMBER}",

                     "packagesIncluded": "*com.kuhniverse.*",

                     "packagesExcluded": "",

                     "filesIncluded": "*.class",

                     "filesExcluded": "*test-classes*",

                     "recursive": true,

                     "includeResources": true,

                     "testStage": "${SUREFIRE_TEST_STAGE}",

                     "failsafeArgLine": "deploy",

                     "labId": "'${param1}'",

                     "executionType": "full",

                     "logEnabled": false,

                     "logDestination": "console",

                     "logLevel": "off",

                     "logFolder": "/tmp",

                     "sealightsJvmParams": {

                         "sl.scm.provider": "test",

                         "sl.scm.baseUrl": "https://{dns}/projects/{project}/repos/{repo}/browse",

                         "sl.scm.version": "4.9.0"},

                     "enabled": true

                }' > slmaven.json

                '''

            }

        }

        stage('Build') {

            steps {

                sh'''

                    labId=$(cat slmaven.json |/usr/local/bin/jq -r '.labId')

                    echo ${labId}

                    /usr/local/bin/mvn ${labId}

                '''

            }

        }

        stage('Test'){

            steps {

                sh '''

                    provider=$(cat slmaven.json |/usr/local/bin/jq -r '.sealightsJvmParams | .sl.scm.provider')

                    /usr/local/bin/mvn ${provider}

                '''

            }

        }

        stage('Deploy') {

            steps {

               sh'''

                    arg=$(cat slmaven.json |/usr/local/bin/jq -r '.failsafeArgLine')

                    echo "can deploy using command: mvn ${arg}"

                '''

            }

        }

    }

}
