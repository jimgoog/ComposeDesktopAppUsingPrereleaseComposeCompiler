# Using Compose Compiler to support different versions of Kotlin

For the purposes of testing, development, or just living on the edge... some people want to use
versions of Kotlin that are not yet officially supported by the Compose Compiler.  For this purpose,
we publish some branches the Compose Compiler that follow various versions of the Kotlin Compiler.

It is worth noting that these builds of the Compose Compiler typically undergo less testing than
the final builds published on Gmaven, so adopt at your own risk.

# Compatibility table

To see a table of Kotlin versions and their matching Compose Compiler versions, check out the compatibility table on androidx.dev:
[https://androidx.dev/storage/compose-compiler/repository](https://androidx.dev/storage/compose-compiler/repository)

# Project Configuration

This repository contains an example Compose project using pre-release builds of both the Kotlin and
the Compose Compiler, while pointing to stable versions of the Compose UI libraries.

Specifically, there are two things to point out:

- [settings.gradle:9](https://github.com/jimgoog/ComposeDesktopAppUsingPrereleaseComposeCompiler/blob/main/settings.gradle#L9) upgrade the compose gardle plugin which includes bug fixes:

  ```
     id("org.jetbrains.compose").version("1.2.0-beta02-dev798")
  ```

- [gradle.properties:3](https://github.com/jimgoog/ComposeDesktopAppUsingPrereleaseComposeCompiler/blob/main/gradle.properties#L3) sets the desired version of Kotlin:

  ```
     kotlin.version=1.7.20
  ```
 - [app/build.gradle:12](https://github.com/jimgoog/ComposeDesktopAppUsingPrereleaseComposeCompiler/blob/main/app/build.gradle#L12) adds the compose compiler pre-release repository:

    ```
    maven("https://androidx.dev/storage/compose-compiler/repository/")
    ```
- [app/build.gradle:29](https://github.com/jimgoog/ComposeDesktopAppUsingPrereleaseComposeCompiler/blob/main/app/build.gradle#L29) swaps the jetbrains ComposeCompiler build with the google ComposeCompiler:

   ```
   // Use the Google-built Compose Compiler pre-release
   allprojects {
     configurations.all {
       resolutionStrategy.dependencySubstitution {
         substitute(module("org.jetbrains.compose.compiler:compiler")).apply {
           using(module("androidx.compose.compiler:compiler:1.4.0-dev-k1.7.20-RC-a143c065804"))
         }
       }
     }
   }
   ```
 - [app/build.gradle:40](https://github.com/jimgoog/ComposeDesktopAppUsingPrereleaseComposeCompiler/blob/main/app/build.gradle#L40) disables the compatibility version check:

    ```
   // Suppress version compatibility check, sicne this pre-release was built for Kotlin 1.7.20-RC
   tasks.withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile> {
     kotlinOptions {
       freeCompilerArgs += listOf(
         "-P",
         "plugin:androidx.compose.compiler.plugins.kotlin:suppressKotlinVersionCompatibilityCheck=true"
       )
     }
   }
   ```

Compose Desktop Application

- `./gradlew run` - run application
- `./gradlew package` - package native distribution into `build/compose/binaries`


