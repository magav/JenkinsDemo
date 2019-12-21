#!groovy

pipeline {
    agent any

    options {
        ansiColor("xterm")
    }
    
    environment {
        def config = readJSON file: 'config.json'
        def isTestStageEnabled = "${config.stages.test.isEnabled}"
        def isTestCoverageStageEnabled = "${config.stages.test.coverage.isEnabled}"
    }

    stages {
        stage('SetUp') {
            steps {
                sh "echo SetUp"
                sh "echo ${config}"
                sh "echo ${myStages}"
            }
        }

        stage('Test') {
            when { expression { "$isTestStageEnabled" == true } }
            steps {
                executeTestStage()
                stages {
                    stage('Coverage') {
                        when { expression { "$isTestCoverageStageEnabled" == true } }
                    }
                }
            }
            post {
                always {
                    // Processing test results
                    junit 'build/results/scan/report.junit'
                }
            }
        }
    }

    post {
        success {
            sh 'echo "success :)"'
        }

        failure {
            sh 'echo "failure :("'
        }
    }
}

void executeTestStage() {
    def config = readJSON file: 'config.json'
    String projectName = "${config.environment.projectName}"
    String sourcePath = "${config.environment.sourcePath}"
    String reportPath = "${config.environment.reportPath}"
    
    sh "echo ProjectName: $projectName SourcePath: $sourcePath ReportPath: $reportPath"
    sh "echo Will start coverage step..."
    sh "bundle exec fastlane coverage projectName:$projectName sourcePath:$sourcePath reportPath:$reportPath"
    sh "bundle exec fastlane test"
}
