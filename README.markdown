A fork of [gitblit] to fix various test failures, and to improve the build infrastructure using Gradle as the build tool.

## Gradle build

Standard Gradle tasks are used in the `build.gradle` script to compile and package Gitblit artefacts. Also, custom Gradle tasks were added to the standard `buildSrc` folder to achieve complex operations which are unique to the Gitblit project, and thus are unavailable in standard Gradle nor in any relevant plugins on the Internet.

Gradle itself need JDK8 or later versions to run, and by default the same JDK is also used by Gradle to compile the Gitblit project's code. The project has been built successfully using OpenJDK8-14 versions, and the resulting Gitblit software artefacts can run with the same Java version range.

If the Java 1.7 runtime is required to run the Gitblit software, the project can be built to be compatible with that Java version, by switching the following properties in `build.gradle` script from the default:
<pre>
//compiles build artefacts to
//run under Java 1.8 and later versions
sourceCompatibility = JavaVersion.VERSION_1_8
targetCompatibility = JavaVersion.VERSION_1_8
</pre>
to 
<pre>
//compiles build artefacts to
//run under Java 1.7 and later versions
sourceCompatibility = JavaVersion.VERSION_1_7
targetCompatibility = JavaVersion.VERSION_1_7
</pre>
and the resulting Gitblit software artefacts can be run with Java 1.7 runtime.

A Gradle sofware installation is not required to run the build script. The *Gradle wrapper* is already included in the project, to automatically download the relevant Gradle software to use, with the `gradlew --version` command.

### Build tasks

The following commands should be executed in the Gitblit folder where the `build.gradle` script resides, to produce the Gitblit software artefacts.

To produce all of the Gitblit artefacts, use the `buildAll` task:

<pre>gradlew buildAll</pre>

The above command will generate all Gitblit software artefacts in the `build/distributions` folder, which include the Gitblit GO, WAR, Manager, Federation Client, API client, as well as the site docs in `build/site` folder.

To build just the Gitblit GO software:

<pre>gradlew buildGO</pre>

and the resulting *gitblit-&lt;version&gt;.zip* (for Windows) and *gitbit-&lt;version&gt;.tar.gz* (for Linux) are produced in the `build/distributions` folder.

Similarly, to build just the site documentation:

<pre>gradlew buildSite</pre>

and the Gitblit html documents are produced in the `build/site` folder, like [this](https://gitblit.github.io/gitblit/).

### IDE support

For the IDE tools such as Eclipse or IDEA development tools, the project can be imported directly into the IDE, as they should understand and act properly on the Gradle build script in the project.

For example, in Eclipse, the Gitblit project can be imported directly as a Gradle project, via the File menu:

<pre>Import --> Gradle --> Existing Gradle Project</pre>

After the import, Eclipse will generate its own `.project` and `.classpath` files for the resultant Eclipse project.

### Testing tasks

With the help of the [TestSets] plugin, test classes in the original `src/test/java` folder were re-organized according to test categories:

* **src/test/java**: the original test folder, for pure unit tests.
  To run these tests, execute the `test` task, i.e. <pre>gradlew test</pre>
* **src/repoTest/java**: for tests requiring some existent local repos but no need for a running gitblit server.
  To run these tests, execute the `repoTest` task, i.e. <pre>gradlew repoTest</pre>
* **src/serverTest/java**: for tests requiring a gitbit server to be running. The current GitBlitSuite test suite class goes here, plus other test classes requiring a gitblit server instance.
  To run these tests, execute the `serverTest` task, i.e. <pre>gradlew serverTest</pre>
* **src/uiTest/java**: for tests of the gitblit web UI (with a gitblit server started during the test run.)
  To run these tests, execute the `uiTest` task, i.e. <pre>gradlew uiTest</pre>
* **src/extServiceTest/java**: for tests requiring some external service to be available e.g. Redis.
  To run these tests, execute the `extServiceTest` task, i.e. <pre>gradlew extServiceTest</pre>

------

## TODO

* [x] Re-creation of the missing hello-world.git repo (used to be at [/git/hello-world.git]). The absence of this repo at GitHub causes a lot of unit test failures in the project.

* [x] Fix various test failures requiring the hello-world repo.

* [x] Zip all required repos used during testing, and check them in to the project, to avoid network fetch from GitHub during testing.

* [x] Re-organize the test classes in src/test according to test categories - the purpose is for ease of test execution and failure investigation:
  * src/test: for pure unit tests.
  * src/repoTest: for tests requiring some existing repos but no need for a gitblit server.
  * src/serverTest: for tests requiring the gitbit server to be running. The current GitBlitSuite class will go here, plus other test classes requiring a gitblit server.
  * src/uiTest: for tests of the gitblit UI.
  * src/extServiceTest: for tests requiring some external service to be available.

* [ ] The above test class re-org requires a new build tool for the project, as Moxie doesn't support it, hence the conversion to Gradle build tool:
  * [x] less radical: initial conversion to Gradle keeping the src/main/java code intact (i.e. no subprojects.)
  * [ ] more radical: re-organize the src/main/java code into subprojects: core, war, go, federation, etc.; each subproject produces its own build artefact:
    * core: would produce the core gitblit.jar library
    * war: would produce gitblit.war which includes the above gitblit.jar
    * go: would produce gitblitGO.zip and/or gitblitGO.tar.gz
    * etc.

* [ ] upgrade to Java 8 as a minimum
  * [x] upgrade selenium to at least version 3, for UI testing
  * [ ] replace [pegdown] (deprecated) library with [flexmark-java] library, to work with Java 8, when generating the docs.

[gitblit]: https://github.com/gitblit/gitblit
[pegdown]: https://github.com/sirthias/pegdown
[flexmark-java]: https://github.com/vsch/flexmark-java
[TestSets]: https://github.com/unbroken-dome/gradle-testsets-plugin

