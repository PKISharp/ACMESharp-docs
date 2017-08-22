---
title: Logging Configuration
---

# Logging Configuration

The ACMESharp PowerShell module uses a logging framework that initializes with a default configuration,
but that allows you to override with your own settings if you choose.

ACMESharp uses the [serilog](https://serilog.net/) structured logging framework.  It is initialized
with a simple console logger that outputs all `Information`-level messages and higher to the standard
output stream.

Logging information is meant to be human-digestable information only, and should typically be used
for debugging and troubleshooting or just confirming correct behavior.

If you wish to taylor the the logging framework configuration, you have the option of overriding
its settings by creating and JSON file in one of these two locations (searched in this order):

* `%LOCALAPPDATA%\ACMESharp\serilog.json`
* `%PROGRAMDATA%\ACMESharp\serilog.json`

The file should contain a JSON object mapping with keys and values defined in similar
fashion to the [AppSettings](https://github.com/serilog/serilog/wiki/AppSettings#configuring-the-logger)
configuration.

For example:

```json
{
  "serilog:using:RollingFile": "Serilog.Sinks.RollingFile",
  "serilog:write-to:RollingFile.pathFormat": "%TEMP%\Logs\myapp-{Date}.txt",
  "serilog:write-to:RollingFile.retainedFileCountLimit": "10"
}
```

Values may contain environment variable references which will be expanded, such as the `%TEMP%` reference
in the example above.
