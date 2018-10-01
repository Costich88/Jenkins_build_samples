node {

    stage('Clean workspace'){
        cleanWs()
    }

    try {
        stage('Preparation (Checking out)') {
            git 'https://github.com/Costich88/xamarin-forms-samples'
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

            step_name="Creating ENV file"
            sh """mkdir fastlane
                touch fastlane/.env.xamarin
                cat <<EOT >> fastlane/.env.xamarin
                BUILD_TASK="msbuild /t:SignAndroidPackage /p:Configuration=Release"
                PATH_TO_APK="sh("find $WORKSPACE -name *Signed.apk")"
                end"""

            step_name = "Creating Fastfile"
            sh """touch fastlane/Fastfile
                cat <<EOT >> fastlane/Fastfile
                platform :android do
                  lane :build_android do
                    sh("/Library/Frameworks/Mono.framework/Versions/Current/Commands/nuget restore $WORKSPACE/RpnCalculator.sln")
                    sh("/Library/Frameworks/Mono.framework/Versions/Current/Commands/#{ENV["BUILD_TASK"]} $WORKSPACE/RpnCalculator/RpnCalculator.Android/RpnCalculator.Android.csproj")
                    ENV["PATH_TO_APK"]= sh("find $WORKSPACE -name *Signed.apk")
                    puts("App is located #{ENV["PATH_TO_APK"]}")
                    sh("$ANDROID_HOME/build-tools/26.0.2/apksigner verify -v #{ENV["PATH_TO_APK"]}")
                  end
                end"""

            step_name = "Runnin fastlane"
            sh "/usr/local/bin/fastlane android build_android --env xamarin" 
       }
    }

    catch (Exception ex) {
        println("Building code failed")
    }

    stage('Archiving artefacts'){
        archiveArtifacts '**/*.apk'
    }

}