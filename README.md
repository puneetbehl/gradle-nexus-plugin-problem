# Sample Gradle Application  
This is  a sample Gradle application describing the problem with gradle-nexus:publish-plugin 


## Steps to Reproduce
1. Create a new Gradle library.
2. Add gradle-nexus:publish-plugin as `api` dependency.
3. Create a new subproject with name "docs".
4. Define a configuration named `documentation` as:
```groovy
configurations {
    documentation
}
```
5. Add subproject "lib" as dependency
```groovy
documentation project(':lib')
```
6. Define a custom `Groovydoc` task named `apiDocs` which uses the configuration classpath to generate docs as:
```groovy
task apidocs(type: Groovydoc, group: 'documentation') {
    source  rootProject.allprojects.collect { project ->
        project.files('src/main/groovy')
    }

    destinationDir = new File(buildDir, 'docs/api')
    docTitle = "Libray ${version}"

    classpath = configurations.documentation
    groovyClasspath = configurations.documentation
}
```
7. Execute `./gradlew :docs:apiDocs`.

## Expected Behaviour

The task `./gradlew :docs:apidocs` should correctly generate API documentation under the folder "docs/build/docs/api/".

## Actual Behavior 

The task execution failed with following error:

```groovy
FAILURE: Build failed with an exception.

* What went wrong:
Execution failed for task ':docs:apidocs'.
> Could not resolve all files for configuration ':docs:documentation'.
    > Could not resolve io.github.gradle-nexus:publish-plugin:1.1.0.
        Required by:
        project :docs > project :lib 
    > Cannot choose between the following variants of io.github.gradle-nexus:publish-plugin:1.1.0:
        - runtimeElements
        - shadowRuntimeElements
    All of them match the consumer attributes:
        - Variant 'runtimeElements' capability io.github.gradle-nexus:publish-plugin:1.1.0:
            - Unmatched attributes:
                - Provides org.gradle.category 'library' but the consumer didn't ask for it
                - Provides org.gradle.dependency.bundling 'external' but the consumer didn't ask for it
                - Provides org.gradle.jvm.version '8' but the consumer didn't ask for it
                - Provides org.gradle.libraryelements 'jar' but the consumer didn't ask for it
                - Provides org.gradle.status 'release' but the consumer didn't ask for it
                - Provides org.gradle.usage 'java-runtime' but the consumer didn't ask for it
                - Provides org.jetbrains.kotlin.localToProject 'public' but the consumer didn't ask for it
                - Provides org.jetbrains.kotlin.platform.type 'jvm' but the consumer didn't ask for it
        - Variant 'shadowRuntimeElements' capability io.github.gradle-nexus:publish-plugin:1.1.0:
            - Unmatched attributes:
                - Provides org.gradle.category 'library' but the consumer didn't ask for it
                - Provides org.gradle.dependency.bundling 'shadowed' but the consumer didn't ask for it
                - Provides org.gradle.libraryelements 'jar' but the consumer didn't ask for it
                - Provides org.gradle.status 'release' but the consumer didn't ask for it
                - Provides org.gradle.usage 'java-runtime' but the consumer didn't ask for it

* Try:
Run with --stacktrace option to get the stack trace. Run with --info or --debug option to get more log output. Run with --scan to get full insights.

* Get more help at https://help.gradle.org

BUILD FAILED in 73ms
2:56:47 PM: Task execution finished ' apidocs'.

```