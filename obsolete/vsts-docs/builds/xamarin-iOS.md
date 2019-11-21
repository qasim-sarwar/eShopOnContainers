# Xamarin iOS Build

Follow these steps to create a VSTS build for your eShopOnContainers app (iOS)

**Note**: This document assumes basic knowledge about creating builds and configuring external VSTS connections 

## Creating the build

Despite the _"Get Sources"_ task there are three tasks more in the build:

1. Build Xamarin iOS Project
2. Copy generated packages
3. Publish the build artifact.

![iOS Build Steps](images/ios-build.png)

Let's discuss each of them.

### Build the project

Add a "Xamarin iOS" task with following configuration:

1. `eShopOnContainers-iOS.sln` in "Solution". This solution has been created ex professo for the build.
2. Ensure that the "Create App Package" checkbox is enabled

**About signing & Provisioning section**

In order to deploy your app to a physical device you must sign it using a certificate with a provisioning profile. Refer to [this blog
post of the Xamarin team](https://blog.xamarin.com/continuous-integration-for-ios-apps-with-visual-studio-team-services/) for more info.

Basically you have three options for setting the certificate (p12 file) and the provisioning profile:

1. Use MacInCloud VSTS agent and setup the p12 file and provisioning profile in the setup [https://blogs.msdn.microsoft.com/visualstudioalm/2015/11/18/macincloud-visual-studio-team-services-build-and-improvements-to-ios-build-support/](https://blogs.msdn.microsoft.com/visualstudioalm/2015/11/18/macincloud-visual-studio-team-services-build-and-improvements-to-ios-build-support/)
2. Use a custom mac machine with the certificate and provisioning profile installed. In this case you don't have to do anything else.
3. Have the p12 file and the provisioning profile reachable on somewhere

If you choose option 3, you need to download the certificate and the provisioning profile into the build agent (using a previous build task).
Once downloaded two files, you have to specify the location of both in the "Signing & Provisioning Section".

![iOS Build Step 1](images/ios-build-step1.png)

### Copy generated files to output folder

Add a "Copy files" task with following configuration:

1. `src/Mobile/eShopOnContainers/eShopOnContainers.iOS/bin/iPhone/$(BuildConfiguration)` in "Source Folder"
2. `**/*.ipa` in "Contents"
3. `$(Build.ArtifactStagingDirectory)` in "Target Folder"
4. Ensure that "Clean Target folder" (under "Advanced" section) is checked

This way we copy the generated IPA in the _Build.ArtifactStagingDirectory_ folder (and remove any previous IPA generated by a previous build).

![iOS Build Step 2](images/ios-build-step2.png)

### Publishing build artifact

Add a "Publish Build Artifacts" task, with following configuration:

1. `$(Build.ArtifactStagingDirectory)` in "Path to publish"
2. `drop` in "Artifact Name"
3. `Server` in "Artifact Type"

![Android Build Step 3](images/ios-build-step3.png)