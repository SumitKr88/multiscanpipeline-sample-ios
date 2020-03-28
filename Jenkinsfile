#!/usr/bin/env groovy

//Figure out if we want a teams channel?
def office365WebhookUrl = "https://outlook.office.com/webhook/a7a0122b-f23f-4ebe-bd15-dc6ab2e3ece8@36da45f1-dd2c-4d1f-af13-5abe46b99921/IncomingWebhook/cafdd0d730b14bfb8a50388090d41b3f/0038a58c-d45d-4c2e-a7f9-21e6106d1e98"

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
    stage('Checkout') {
      steps {
        step([$class: 'StashNotifier'])
      }
    }

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
        // uncomment the line below and as Reggie Fenton, Scott Kriss or Jesse Le for the Jenkins pw, run once with this unlock line.
        //sh "security unlock-keychain -p <password> login.keychain"
        //sh "xcodebuild -workspace Experience.xcworkspace -scheme Experience -sdk iphoneos -configuration \"Debug\" archive -archivePath ${WORKSPACE}/build/Experience.xcarchive"
        sh "xcodebuild -workspace AdManagerTest.xcworkspace -scheme AdManagerTest -sdk iphoneos -configuration \"Debug\" CODE_SIGN_IDENTITY=\"\" CODE_SIGNING_REQUIRED=NO CODE_SIGN_ENTITLEMENTS=\"\" CODE_SIGNING_ALLOWED=\"NO\" build"
      }
    }
    stage('Tests') {
      steps {
        sh "xcodebuild -workspace AdManagerTest.xcworkspace -scheme AdManagerTest -sdk iphonesimulator -configuration \"Debug\" CODE_SIGN_IDENTITY=\"\" CODE_SIGNING_REQUIRED=NO CODE_SIGN_ENTITLEMENTS=\"\" CODE_SIGNING_ALLOWED=\"NO\" -destination \"platform=iOS Simulator,name=iPhone 11 Pro,OS=13.1\" test"
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
