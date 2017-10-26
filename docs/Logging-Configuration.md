---
title: Logging Configuration
---

# Logging Configuration

The ACMESharp PowerShell module uses a logging framework that initializes with a default configuration,
but that allows you to override with your own settings if you choose.

ACMESharp uses the [NLog](http://nlog-project.org/) logging framework.  By default, it is
configured with a simple console logger that outputs all `Information`-level messages and
higher to the standard output stream.

Logging information is meant to be human-digestable information only, and should typically be used
for debugging and troubleshooting or just confirming correct behavior.

If you wish to taylor the the logging framework configuration, you have the option of overriding
its settings by creating an XML file in one of these two locations (searched in this order):

* `%LOCALAPPDATA%\ACMESharp\nlog.config`
* `%PROGRAMDATA%\ACMESharp\nlog.config`

The file should contain an XML configuration based on NLog's
[*simplified configuration*](https://github.com/nlog/nlog/wiki/Configuration-file#configuration-file-format)
file format (i.e. the standalone XML configuration format).

For example:

```xml
<nlog autoReload="true">
  <variable name="logDirectory" value="${basedir}/logs/${shortdate}"/>
  <targets>
    <target name="file1" xsi:type="File" fileName="${logDirectory}/file1.txt"/>
    <target name="file2" xsi:type="File" fileName="${logDirectory}/file2.txt"/>
    <target name="async-retry-file" xsi:type="AsyncWrapper">
      <target xsi:type="RetryingWrapper">
        <target xsi:type="File" fileName="${machine}-best-effort.txt" />
      </target>
    </target>
  </targets>
</nlog>
```

NLog's configuration mechanism is quite powerful and includes support for evaluating embedded
environment variables.
