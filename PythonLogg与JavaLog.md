#Python logging 与 Java Logging
Python.ini 

```  
 [loggers]
 keys=root,sender

 [handlers]
 keys=tmp,sender_debug,sender_info,sender_error

 [formatters]
 keys=debug_formatter,info_formatter,error_formatter

 [logger_root]
 level=DEBUG
 handlers=tmp

 [logger_sender]
 level=DEBUG
 handlers=sender_debug,sender_info,sender_error
 qualname=sender
 propagate=1

 [handler_tmp]
 class=FileHandler
 level=DEBUG
 formatter=debug_formatter
 args=('./log/root.log','a+')

 [handler_sender_debug]
 class = FileHandler
 level = DEBUG
 formatter = debug_formatter
 args = ( './log/sender_debug.log', 'a+')

 [handler_sender_info]
 class = FileHandler
 level = INFO
 formatter = info_formatter
 args = ( './log/sender_info.log', 'a+')

 [handler_sender_error]
 class = FileHandler
 level = ERROR
 formatter = error_formatter
 args = ( './log/sender_error.log', 'a+')

 [formatter_debug_formatter]
 format=%(asctime)s - %(name)s - %(levelname)s - %(message)s

 [formatter_info_formatter]
 format=%(asctime)s - %(name)s - %(levelname)s - %(message)s

 [formatter_error_formatter]
 format=%(asctime)s - %(name)s - %(levelname)s - %(message)s
```  
 