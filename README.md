# DroidAssist ![license](http://img.shields.io/badge/license-Apache2.0-brightgreen.svg?style=flat) ![Release Version](https://img.shields.io/badge/release-1.0.9-blue.svg)

`DroidAssist` is a lightweight Android Studio gradle plugin based on Javassist for editing bytecode in Android. Unlike other AOP solutions, DroidAssist provides a more lightweight, easy-to-use, non-intrusive, configurable bytecode operation. Even without any Java bytecode knowledge, developers can modify a class file dynamically by editing xml plugin configuration only. You can use AOP-style functionality by adding simple Java code without introducing additional dependencies.

[中文说明](README_CN.md)

## Features

* With one config, All bytecodes can be processed.
* Rich bytecode modification, such as Insert, Replace, Around and so on.
* Only one plugin, no extra dependencies.
* Optimized build speed. DroidAssist takes up little build time.

## Quick Start

DroidAssist is available for application model or library model in Android Studio. You need to add the plugin and edit a proprietary xml plugin configuration.

##### 1. Configure your project-level  `build.gradle`  to include DroidAssist plugin:

```groovy
dependencies {
    classpath "com.didichuxing.tools:droidassist:1.0.9"
}
```

##### 2. Then, apply the DroidAssist plugin in your android project module's  `build.gradle`:

```groovy
apply plugin: 'com.didichuxing.tools.droidassist'
droidAssistOptions {
    config file("droidassist.xml"), file("droidassist2.xml")// Point to the DroidAssist config files. (required)
}
```

Other plugin options:

* `enable `  Enable plugin or not, Default value is `true`. (optional)
* `logLevel` For control plugin's log level: `0` for no log, `1` for log to console, `2` for log to file, `3` for log to file and console. Default value is `1`. (optional)
* `logDir` Directory of plugin's log output, default value is `build/outputs/logs`  (optional)


## Sample Usage

Logging is an important component of the software development. The following xml plugin configuration replaces the `d` method of `android.util.Log`  with custom `log` method in which you can manage log dynamically such as replacing log tag, revising log level and so on.

```xml
<Replace>
    <MethodCall>
        <Source>
           int android.util.Log.d(java.lang.String,java.lang.String)
        </Source>
        <Target>
            $_=com.didichuxing.tools.test.LogUtils.log($1,$2);
        </Target>
    </MethodCall>
</Replace>
```

Class before process:

```java
public class MainActivity extends Activity {
    public static final String TAG = "MainActivity";

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        Log.d(TAG, "MainActivity onCreate"); // The source method using android.util.Log(..)
    }
}
```

Class after process:

```java
public class MainActivity extends Activity {
    public static final String TAG = "MainActivity";

    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        String var2 = "MainActivity";
	    String var3 = "MainActivity onCreate";
        int var4 = LogUtils.log(var2, var3); // The target method using custom log method.
    }
}
```

## Full documentation
For full documentation and advanced configuration, see [wiki](docs/wiki.md).

## Limitations

1. Due to the use of Javassist to process bytecode, there will be a few useless local variables in class files processed by DroidAssist.

2. For the need to  process bytecode, DroidAssist may add some extra intermediate methods In some scenarios.

3. When used in library model, DroidAssist can handle classes generated by Java source code only (The jars which are dependent locally will not be handled).


## License
See [LICENSE](LICENSE)
