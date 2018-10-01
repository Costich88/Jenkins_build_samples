node {

    stage('Clean workspace'){
        cleanWs()
    }

    try {
        stage('Preparation (Checking out)') {
            git 'https://github.com/BuddyBuild/2048-Android'
        }
    }

    catch (Exception ex) {
        println("Checkout failed")
    }
    
    try {
        stage('Building code'){
            step_name = "Export env variables"
            sh """export LC_ALL=en_US.UTF-8
                  export LANG=en_US.UTF-8"""

            step_name = "Creating Fastfile"
            sh """mkdir -p 2048/fastlane
                touch 2048/fastlane/Fastfile
                cat <<EOT >> 2048/fastlane/Fastfile
                platform :android do
                  lane :build_android do
                    gradle(task: "build")
                    sh("$ANDROID_HOME/build-tools/26.0.2/zipalign -v 4 $WORKSPACE/2048/2048/build/outputs/apk/2048-release-unsigned.apk $WORKSPACE/2048/2048/build/outputs/apk/2048.apk ")
                    sh("$ANDROID_HOME/build-tools/26.0.2/apksigner sign --ks /Users/kanstantsin/test --ks-pass pass:test228 --ks-key-alias newtest $WORKSPACE/2048/2048/build/outputs/apk/2048.apk")
                    sh("$ANDROID_HOME/build-tools/26.0.2/apksigner verify -v $WORKSPACE/2048/2048/build/outputs/apk/2048.apk")
                    ENV["APK"]= sh("find $WORKSPACE -name 2048.apk")
                    puts("App is located #{ENV["APK"]}")
                  end
                end"""

            step_name = "Runnin fastlane"
            sh """cd 2048
                  chmod +x gradlew
                  /usr/local/bin/fastlane android build_android"""        }
    }

    catch (Exception ex) {
        println("Building code failed")
    }

}
