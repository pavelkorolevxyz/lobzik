![Gradle Plugin Portal](https://img.shields.io/gradle-plugin-portal/v/xyz.mishkun.lobzik?style=flat-square&versionPrefix=0.3&versionSuffix=.0)

# Lobzik: Continuous Modularisation Toolkit

Lobzik is a gradle plugin designed to help Android developers chop their monolithic codebases into smaller pieces. This
powerful tool uses the Louvain algorithm to analyze your project's class-dependency graph and identify potential modules
for extraction. With conductance score as its guiding metric, Lobzik ensures that the modules it suggests are sorted by
effort. Lobzik outputs a detailed report of your potential improvements in neat HTML one-pager, allowing you to quickly
decide which modules to extract and in which order.

## Getting started

To use Lobzik in your project, follow these steps:

### Step 1: Add the Lobzik plugin to your Gradle build

To use Lobzik, add the following to your root `build.gradle.kts`:

```kotlin
plugins {
    id("xyz.mishkun.lobzik") version "0.3.0"
}
```

### Step 2: Configure the Lobzik plugin

You can configure Lobzik by using `lobzik` extension in your root `build.gradle.kts` file:

```kotlin
lobzik {
    // name of your monolith module for report
    monolithModule.set("app") 
    // names of your feature modules (you can use regex here)
    featureModulesRegex.add("feature-.*")
    // package prefix for your modules to filter only your own code and exclude code of deps like java.util.*, androidx.*, etc.
    packagePrefix.set("xyz.mishkun")
    // list of regexes of ignored classes. Add your glue code patterns here 
    ignoredClasses.addAll(".*Command$", ".*Coordinator$",  "^MyApplication$")
}
```

(See [Using Lobzik Effectively](#using-lobzik-effectively) for more info about parameters)

### Step 3: Run the Lobzik report task

Then, run the `lobzikReport` task:

```shell
./gradlew lobzikReport
```

### Step 4: Enjoy your report

Open report at `rootProj/build/reports/lobzik/analysis/report.html` and have fun extracting modules!

## Using Lobzik Effectively

Okay, but how Lobzik actually works?

### Configuration Options

To use Lobzik effectively, you need to configure it with the appropriate settings. Here are the available configuration
parameters and some examples of how to use them:

- `monolithModule` parameter is used to specify the name of your monolith module, which will be included in the report
  generated by Lobzik
- `featureModulesRegex` parameter is a list of regular expressions that matches the names of your feature modules. You
  can use regex to include multiple modules at once.
- `packagePrefix` parameter is used to filter your codebase and exclude code from external dependencies
  like `java.util.*` or `androidx.*`. Library code tangles your dependency graph and interferes with Louvain algorithm.
- `ignoredClasses` parameter is a list of regular expressions that matches the names of the classes you want Lobzik to
  ignore when analyzing your project's dependency graph. This is useful for excluding glue code patterns or other
  classes that represent module boundaries. For example, DI code, navigation commands and other means of gluing modules.
  To give you a hint, html report includes a list of classes with high rate of incoming and outcoming dependencies. Be
  sure to check them out and add the generic ones to the ignored list.

Modules not listed in `monolithModule` or `featureModulesRegex` are treated as core modules and
are ignored during the analysis. Core modules tangle the code structure and interfere with Louvain algorithm.

### Interpreting the Lobzik Report

The Lobzik report consists of four parts. Here's a
brief overview of each part:

#### Core Candidates

It lists classes with high in or out degrees. These classes are the best candidates for
extracting to core modules or to adding into `ignoredClasses` list.
Inspect these classes carefully and extract or ignore generic classes first, then rerun the report. Here you will also
find feature-specific classes with high in or out degree. So not all classes here should be extracted or ignored.

#### Monolith Modules

This section lists all detected modules with their corresponding conductance and cut scores. Conductance score measures
how much code will be extracted divided by the cut score, while cut score measures how much pure effort you will need to put
into extracting it.

#### Module Graphs

In this section, Lobzik provides a graph for each module that shows its dependencies. It also lists all the classes in
the module and shows the cuts that Lobzik recommends to create new modules.

#### Whole Graph

Finally, the report includes a graph of the all detected modules and its dependencies, which can be helpful in visualizing the overall structure
of your project.

## Exploring dependency graph yourself

You might find that lobzik report is not enough and you want to poke dependency graph yourself. I've got you covered! 

### Using gephi

Gephi is an open source graph visualization and analysis tool. To open report in gephi,
open `build/reports/lobzik/analysis/io_gexf.gexf` file and explore your codebase's dependency graph yourself!

### Using third-party tool

Open graphML file `build/reports/lobzik/analysis/io_graphml.graphml` in any third-party tool, jupyter notebook or other. Sky is limit!

## Contributing

If you would like to contribute to the development of Lobzik, please submit issues or pull requests.

## Credits

- [Gephi](http://gephi.org) and Gephi Toolkit for powering this project's graph analysis and visualization algorithms
- [dependency-analysis-android-gradle-plugin](https://github.com/autonomousapps/dependency-analysis-android-gradle-plugin) for dependency extraction algorithm

## License

Lobzik is licensed under the MIT License.
