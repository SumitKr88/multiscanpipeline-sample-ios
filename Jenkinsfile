#!/usr/bin/env groovy

//Figure out if we want a teams channel?
def office365WebhookUrl = "INSERT_YOUR_WEB_HOOK_URL"

pipeline {
  agent any

  environment {
    CI = 'CI'
    DEVELOPER_DIR = '/Applications/Xcode.app/Contents/Developer/'
  }

  options {
    disableConcurrentBuilds()
  }

  stages {

    stage('Dot Files Check') {
      steps {
        sh "if [ -e .gitignore ]; then echo '.gitignore found'; else echo 'no .gitignore found' && exit 1; fi"
      }
    }

    stage('Build') {
      steps {
        sh "echo | xcodebuild -version"
        sh "xcodebuild -workspace AdManagerTest.xcworkspace -scheme AdManagerTest clean"

        // If you encounter a build error "errSecInternalComponent Command PhaseScriptExecution failed with a nonzero exit code" with your framework,
        //sh "security unlock-keychain -p <password> login.keychain"
        //sh "xcodebuild -workspace Experience.xcworkspace -scheme Experience -sdk iphoneos -configuration \"Debug\" archive -archivePath ${WORKSPACE}/build/Experience.xcarchive"
        sh "xcodebuild -workspace AdManagerTest.xcworkspace -scheme AdManagerTest -sdk iphoneos -configuration \"Debug\" CODE_SIGN_IDENTITY=\"\" CODE_SIGNING_REQUIRED=NO CODE_SIGN_ENTITLEMENTS=\"\" CODE_SIGNING_ALLOWED=\"NO\" build"
      }
    }
    stage('Tests') {
      steps {
        sh "xcodebuild -workspace AdManagerTest.xcworkspace -scheme AdManagerTestTests -sdk iphonesimulator -configuration \"Debug\" CODE_SIGN_IDENTITY=\"\" CODE_SIGNING_REQUIRED=NO CODE_SIGN_ENTITLEMENTS=\"\" CODE_SIGNING_ALLOWED=\"NO\" -destination \"platform=iOS Simulator,name=iPhone 8,OS=13.3\" test"
      }
    }
}

  post {
   success {
     office365ConnectorSend color: '#86BC25', status: currentBuild.result, webhookUrl: office365WebhookUrl
   }
  //  always {
  //      recordIssues tool: swiftLint(pattern: 'checkstyle.xml'), healthy: 0, unhealthy: 1
  //      sh "swiftlint --strict"
  //  }
   failure {
     office365ConnectorSend color: '#ff0000', status: currentBuild.result, webhookUrl: office365WebhookUrl
   }
  }
}
