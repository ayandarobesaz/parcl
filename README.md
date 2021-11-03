parcl
==========

[![Build Status](https://travis-ci.org/mini2Dx/parcl.svg?branch=master)](https://travis-ci.org/mini2Dx/parcl)

Gradle plugin for bundling your Java application as Windows, Mac and Linux native applications

The plugin can generate the following platform natives:
 * Windows - .exe
 * Mac OS X - .app
 * Linux - .sh script
 
Future releases may add more native formats.

## How to use

Add the following buildscript configuration to the top of your build.gradle

```gradle
buildscript {
    repositories {
        mavenLocal()
        mavenCentral()
    }
    dependencies {
        classpath group: 'org.mini2Dx', name: 'parcl', version: '1.8.0'
    }
}
```

Then add the plugin to your project, configuration for your main class and how you want parcl to bundle the application. The following shows the minimum required configuration.

```gradle
project(":projectName") {
   apply plugin: "java"
   apply plugin: "application"
   apply plugin: "org.mini2Dx.parcl"
   
   ........

   mainClassName = "com.example.MyMainClass"

   parcl {
      exe {
         exeName = "myapplication"
      }
		
      app {
         appName = "My Application"
         icon = "relative/path/to/icon.icns"
         applicationCategory = "public.app-category.adventure-games"
         displayName = 'My Application'
         identifier = 'com.example.my.apple.identifier'
         copyright = 'Copyright 2015 Your Name Here'
      }
		
      linux {
         binName = "myapplication"
      }
   }
}
```

__Note:__ If your version of Gradle gives an error regarding mainClassName, the following workaround has been reported to resolve it:

```gradle
project(":projectName") {
   apply plugin: "java"
   apply plugin: "application"
   apply plugin: "org.mini2Dx.parcl"
   
   ........

   project.getConvention().getPlugin(ApplicationPluginConvention.class).setMainClassName("com.example.MyMainClass")
```

The plugin will add a task called 'bundleNative' to your project. This must be invoked on the platform you wish to bundle the application for, i.e. You must be on Mac OS X to bundle a Mac application.

```bash
gradle clean build bundleNative
```

Depending on your platform, the resulting application bundle will appear in build/windows, build/mac or build/linux.

## Tasks

| Task | Description |
|---|---|
| bundleNative  |  Generates the native application for the current platform |
| bundleNativeZip | Generates the native application and packages it into a .zip  |

## Advanced Configuration

There are several optional configuration parameters for each platform.

| Optional Parameter  | Description | Example |
| ------------- | ------------- | ------------- |
| zipName | Specifies the name for the outputted zip file  | zipName = 'my-game-windows.zip' |
| vmArgs | Passes JVM options to the application on launch  | vmArgs = ["-Xmx1g"] |
| appArgs  | Passes application arguments to the application on launch  | appArgs = ["arg1", "arg2"] |
| withJre  | Copies your local JRE and bundles it with the application. The value of $JAVA_HOME must be passed as an argument. | withJre("/usr/lib/jvm/java-8-oracle/") |

The following example shows all options in use.

```gradle
mainClassName = "com.example.MyMainClass"

parcl {
  exe {
    vmArgs = ["-Xmx1g"]
    appArgs = ["arg1", "arg2"]
    exeName = "myapplication"
    zipName = 'my-game-windows.zip'
			
    withJre("C:\\Program Files (x86)\\Java\\jdk1.8.0_25\\jre")
  }
		
  app {
    vmArgs = ["-Xmx1g"]
    appArgs = ["arg1", "arg2"]
    appName = "My Application"
    icon = "relative/path/to/icon.icns"
    applicationCategory = "public.app-category.adventure-games"
    displayName = 'My Application'
    identifier = 'com.example.my.apple.identifier'
    copyright = 'Copyright 2015 Your Name Here'
    zipName = 'my-game-mac.zip'

    withJre("/Library/Java/JavaVirtualMachines/jdk1.8.0_25.jdk/Contents/Home")
  }
		
  linux {
    vmArgs = ["-Xmx1g"]
    appArgs = ["arg1", "arg2"]
    binName = "myapplication"
    preferSystemJre = true
    zipName = 'my-game-linux.zip'
			
    withJre("/usr/lib/jvm/java-8-oracle/")
  }
}
```

