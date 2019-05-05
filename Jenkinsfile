pipeline {
    options{
        timeout(time:1,unit:'HOURS')
    }
    environment {
        docker_image_name = "java8-maven3-junit5"
    }
    
    agent {
    dockerfile {
        additionalBuildArgs '--no-cache=true --build-arg "JENKINS_USER_ID=112" --build-arg "JENKINS_GROUP_ID=117"'
        args '-v ${PWD}/.m2:/usr/share/maven/.m2'
        dir '.'
        filename 'Dockerfile'
        label env.docker_image_name
        }
    }
    stages {
        stage('maven execution') {
            steps {
                script {
                    dir('.') {
                        sh 'set HTTP_PROXY=$HTTP_PROXY'
                        sh 'set HTTPS_PROXY=$HTTP_PROXY'
                        sh 'id'
                        sh 'mvn clean package site'
                    }
                }
            }
        }
        stage('Analysis') {
            steps {
                script {
                    dir('.') {
                        sh 'echo "Analysis stage"'
                    }
                }
            }
        }
    }
    post {
        always {
            recordIssues enabledForFailure: true, tool: checkStyle(pattern: 'target/checkstyle-result.xml')
            recordIssues enabledForFailure: true, tool: spotBugs(pattern: 'target/spotbugsXml.xml')
            stepcounter settings: [[encoding: 'UTF-8', filePattern: 'src/main/**/*.java', filePatternExclude: '', key: 'main'], [encoding: 'UTF-8', filePattern: 'src/test/**/*.java', filePatternExclude: '', key: 'test']]
            publishCoverage adapters: [[jacocoAdapter path: 'target/site/jacoco/jacoco.xml']]
        }
    }
}
