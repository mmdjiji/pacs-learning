# Build Plugins


[Guide Document](https://nroduit.github.io/en/basics/customize/build-plugins/) |
[Example: nroduit/weasis-isowriter](https://github.com/nroduit/weasis-isowriter)

## List of plug-ins types
* Media viewer or editor (main central panel that implements `ViewerPlugin` or `ImageViewerPlugin` and the factory implements `SeriesViewerFactory`)
* Toolbar associated with a viewer (implements `Toolbar`)
* Tool associated with a viewer (right panel that implements `DockableTool`)
* Data Explorer (data model implements `DataExplorerModel` and data view implements `DataExplorerView`, and the factory implements `DataExplorerViewFactory`)
* Import data into an explorer (ex. `ImportDicom` and the factory implements `DicomImportFactory`)
* Export data into an explorer (ex. `ExportDicom` and the factory implements `DicomExportFactory`)
* DICOM editor or viewer for special modalities (`DicomSpecialElementFactory` and `SeriesViewerFactory`), see weasis-dicom-sr
* Media codec (implements `Codec`)
* Preferences (implements `PreferencesPageFactory`)
* UI aggregator. This is the application main user interface bundle. The maven artifact of this bundle must be defined in config.properties (ex. weasis.main.ui=weasis-base-ui)

## Install archetype

```bash
$ git clone https://github.com/nroduit/Weasis.git
$ cd Weasis/archetype
$ mvn install
```
If error, try to upgrade your JDK version.

Maybe you can try to skip enforcer also:
```bash
$ mvn clean install -Denforcer.skip=true
```
Or continue build even if error:
```bash
$ mvn clean install -Denforcer.fail=false
```

## Build plug-ins from Maven archetype

```bash
$ mvn archetype:generate -DarchetypeCatalog=local
```

If error as `The desired archetype does not exist (org.weasis.samples:weasis-plugin-base-viewer-archetype:4.0.0-RC)`, try to run `mvn install` in  `Weasis/`.

Then run:
```bash
$ mvn compile
```

I met an error at the first compilation:
```
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.9.0:compile (default-compile) on project test: Compilation failure: Compilation failure:        
[ERROR] SampleToolFactory.java:[1,1] need class, interface, enum or record
[ERROR] SampleToolFactory.java:[1,6] invalid token: '#'
```

Then I found something that I cannot understand at the `SampleToolFactory.java`:
```java
set( # = '#' )
```

After I delete it, everything is okay.

## Package

When you want to package it into a `jar` file, run the following command:
```bash
$ mvn package
```

Then you will found `target/test-1.0-SNAPSHOT.jar`, copy it into `app/plugins`.

## Installation

The file `app/conf/ext-config.properties`( For me, the full path is `C:\Program Files\Weasis\app\conf\ext-config.properties` ) must be adapted and plug-ins must be placed in the directory `app/plugins`, like this:
```properties
felix.auto.start.85=${weasis.codebase.url}/plugins/test-1.0-SNAPSHOT.jar
```
In the path, `test-1.0-SNAPSHOT.jar` is your file name with extension.

!!!question
    Why we must do this to lock the filename or the installation path of my plugin `jar` file? Why the main program cannot load it automatically when found a `jar` file in the plugin folder?

