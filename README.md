# TruEra

TruEra specific changes are in the `truera` branch.

## Merge latest changes from upstream
Fetch all tags (which will be releases) from upstream and push it to TruEra fork.
```bash
git fetch upstream --tags
git push origin --tags
```

Update the `truera` branch with `master`.
```bash
git checkout master
git merge upstream/master
git push origin HEAD
git checkout truera
git rebase -i origin/master
```

We will keep the changes to minimum and hoping that there won't be any merge conflicts.

## Generating a new build from a new release

Official source for each release is maintained as a git tag. When generating a new TruEra version using a particular release
start by checking out the tag as a branch. For instance, for v1.15.0 you will need:

```bash
git checkout -b release/v1.15.0 v1.15.0
```

Then cherry-pick the TruEra specific changes onto this branch. Currently there is only one change that needs to be cherry-picked.

```bash
git cherry-pick 0f8d45adec4032552bbc0f9c6a26815863b8b273
git push --set-upstream origin release/v1.15.0
```

Build the source code:
NOTE: Since we are increasing the line length, a lot of tests will fail legitimately, please ignore those.

```bash
mvn clean install -DskipTests
```

The Jar will be generated in `core/target/google-java-format-<verssion>-all-deps.jar`.


# google-java-format

`google-java-format` is a program that reformats Java source code to comply with
[Google Java Style][].

[Google Java Style]: https://google.github.io/styleguide/javaguide.html

## Using the formatter

### from the command-line

[Download the formatter](https://github.com/google/google-java-format/releases)
and run it with:

```
java -jar /path/to/google-java-format-${GJF_VERSION?}-all-deps.jar <options> [files...]
```

The formatter can act on whole files, on limited lines (`--lines`), on specific
offsets (`--offset`), passing through to standard-out (default) or altered
in-place (`--replace`).

To reformat changed lines in a specific patch, use
[`google-java-format-diff.py`](https://github.com/google/google-java-format/blob/master/scripts/google-java-format-diff.py).

***Note:*** *There is no configurability as to the formatter's algorithm for
formatting. This is a deliberate design decision to unify our code formatting on
a single format.*

### IntelliJ, Android Studio, and other JetBrains IDEs

A
[google-java-format IntelliJ plugin](https://plugins.jetbrains.com/plugin/8527)
is available from the plugin repository. To install it, go to your IDE's
settings and select the `Plugins` category. Click the `Marketplace` tab, search
for the `google-java-format` plugin, and click the `Install` button.

The plugin will be disabled by default. To enable it in the current project, go
to `File→Settings...→google-java-format Settings` (or `IntelliJ
IDEA→Preferences...→Other Settings→google-java-format Settings` on macOS) and
check the `Enable google-java-format` checkbox. (A notification will be
presented when you first open a project offering to do this for you.)

To enable it by default in new projects, use `File→Other Settings→Default
Settings...`.

When enabled, it will replace the normal `Reformat Code` action, which can be
triggered from the `Code` menu or with the Ctrl-Alt-L (by default) keyboard
shortcut.

The import ordering is not handled by this plugin, unfortunately. To fix the
import order, download the
[IntelliJ Java Google Style file](https://raw.githubusercontent.com/google/styleguide/gh-pages/intellij-java-google-style.xml)
and import it into File→Settings→Editor→Code Style.

### Eclipse

The latest version of the `google-java-format` Eclipse plugin can be downloaded
from the [releases page](https://github.com/google/google-java-format/releases).
Drop it into the Eclipse
[drop-ins folder](http://help.eclipse.org/neon/index.jsp?topic=%2Forg.eclipse.platform.doc.isv%2Freference%2Fmisc%2Fp2_dropins_format.html)
to activate the plugin.

The plugin adds a `google-java-format` formatter implementation that can be
configured in `Window > Preferences > Java > Code Style > Formatter > Formatter
Implementation`.

### Third-party integrations

*   Gradle plugins
    *   [spotless](https://github.com/diffplug/spotless/tree/main/plugin-gradle#google-java-format)
    *   [sherter/google-java-format-gradle-plugin](https://github.com/sherter/google-java-format-gradle-plugin)
*   Apache Maven plugins
    *   [spotless](https://github.com/diffplug/spotless/tree/main/plugin-maven#google-java-format)
    *   [spotify/fmt-maven-plugin](https://github.com/spotify/fmt-maven-plugin)
    *   [talios/googleformatter-maven-plugin](https://github.com/talios/googleformatter-maven-plugin)
    *   [Cosium/maven-git-code-format](https://github.com/Cosium/maven-git-code-format):
        A maven plugin that automatically deploys google-java-format as a
        pre-commit git hook.
*   SBT plugins
    *   [sbt/sbt-java-formatter](https://github.com/sbt/sbt-java-formatter)
*   [maltzj/google-style-precommit-hook](https://github.com/maltzj/google-style-precommit-hook):
    A pre-commit (pre-commit.com) hook that will automatically run GJF whenever
    you commit code to your repository
*   [Github Actions](https://github.com/features/actions)
    *   [googlejavaformat-action](https://github.com/axel-op/googlejavaformat-action):
        Automatically format your Java files when you push on github

### as a library

The formatter can be used in software which generates java to output more
legible java code. Just include the library in your maven/gradle/etc.
configuration.

`google-java-format` uses internal javac APIs for parsing Java source. The
following JVM flags are required when running on JDK 16 and newer, due to
[JEP 396: Strongly Encapsulate JDK Internals by Default](https://openjdk.java.net/jeps/396):

```
--add-exports jdk.compiler/com.sun.tools.javac.api=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.file=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.parser=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.tree=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.util=ALL-UNNAMED
```

#### Maven

```xml
<dependency>
  <groupId>com.google.googlejavaformat</groupId>
  <artifactId>google-java-format</artifactId>
  <version>${google-java-format.version}</version>
</dependency>
```

#### Gradle

```groovy
dependencies {
  implementation 'com.google.googlejavaformat:google-java-format:$googleJavaFormatVersion'
}
```

You can then use the formatter through the `formatSource` methods. E.g.

```java
String formattedSource = new Formatter().formatSource(sourceString);
```

or

```java
CharSource source = ...
CharSink output = ...
new Formatter().formatSource(source, output);
```

Your starting point should be the instance methods of
`com.google.googlejavaformat.java.Formatter`.

## Building from source

```
mvn install
```

## Contributing

Please see [the contributors guide](CONTRIBUTING.md) for details.

## License

```text
Copyright 2015 Google Inc.

Licensed under the Apache License, Version 2.0 (the "License"); you may not
use this file except in compliance with the License. You may obtain a copy of
the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
License for the specific language governing permissions and limitations under
the License.
```
