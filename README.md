# clogger
### A logging library for C programs.

#### Introduction

clogger is a light-weight thread safe logging library designed to run with negligible overhead. It is simple to use and can be used inside any C program. It is just dependent on the standard library which is usually (or can be) installed on most operating systems. At present, clogger has been tested with glibc on Linux. The source is available here.

#### The interface

To use clogger with your program, a single header file has to be included - the `clogger.h` file. There are just three functions that a client / application program needs to call:

*    `int clogger_init(void)`

This function has to be called to initialize variables to be used by clogger at run-time. Multiple calls to this function will be ignored.

*    `int clogger_log(char *, uint8_t)`

Takes a c-string and it's priority level and prints the message to the application's log file. The defined priority levels are:

`OFF`      - The message will not be logged.

`INFO`    - Any message or information.

`ERR`      - Error with which the program can still continue.

`FATAL`  - The program should (ideally) be aborted / handled by the application. But, clogger will not stop the application.


*    `int clogger_terminate(void)`            

Closes files, releases resources and shuts down clogger.

#### The runtime.conf file

The `conf/runtime.conf` file contains some configuration parameters or switches. These switches are required by clogger to configure itself at run-time. The `#` character is used for commenting a sentence. Currently, we can have `#` only as the first character of a sentence. As this file is read every time when clogger initializes, a compilation is not required if changes are made to this file. There are three switches present in this file:

*    `DEBUG_LEVEL`

The `DEBUG_LEVEL` switch can be thought of as a filter. This determines the level at which clogger should capture log messages. It can have one of (or, the result of adding any of) the following values:

`0` - (OFF) no logs will be generated. The log file will not even be created.

`1` - (INFO) any message or information will be captured (along with all higher levels).

`2` - (ERR) error with which the program can still continue will be captured (along with all higher levels)

`4` - (FATAL) the program should be aborted / handled by the application.

These levels correspond to the levels passed to `clogger_log()`. Setting `DEBUG_LEVEL` to  `0` (OFF) in this file will turn off all logging for the application. Any debug level other than `0` (OFF) will activate the appropriate level. For example, if `DEBUG_LEVEL` is set to `2`, the application log will capture messages what were passed to `clogger_log` with the `ERR` level. If `DEBUG_LEVEL` is set to `3`, the messages with `ERR` and `INFO` levels will be captured.

*    `WRITE_METHOD`

Determines whether clogger will clear the contents of the application's log file before starting to log messages. `WRITE_METHOD` can have two values:

`0` - (Overwrite) clears the log file and start logging.

`1` - (Append) preserve previous content and append.

*    `LOG_FILE_PATH`

Denotes the file where the application's log file will be written.

Comments in this file explain more about each switch.

#### What if something inside clogger fails?

clogger will not stop the client application in any case. If the application's log file does not have what is expected or the log file is not generated, please check `clogger.log`. This is clogger's own log file. This file is created (provided there is write access) in the directory from where the client executable is invoked.

#### A sample program
A small and simple program can be found in `src/main.c`.

#### What will a log file look like?

The following is a sample log file of some application:

    [2019-03-24 13:44:44]  [INFO   ]  src/main.c:17  some info message
    [2019-03-24 13:44:44]  [ERR    ]  src/main.c:18  some err message
    [2019-03-24 13:44:44]  [UNKNOWN]  src/main.c:19  some unk message
    [2019-03-24 13:44:44]  [FATAL  ]  src/main.c:20  some fatal message
    [2019-03-24 13:44:44]  [INFO   ]  src/main.c:21  another info message
    
#### Known issues
* clogger has not been compiled or tested on platforms other than Linux.
* More checks and validations are required on the data read from the runtime.conf file. For example, checking for tabs and special characters.

#### Room for improvement
* Provide an alternative to set everything by calling functions instead the property file.
* Buffering - enable / disable.
* Users should be able to use modifiers like %s or %d directly in the log messages.
