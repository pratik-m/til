# Redirect the python `print` command to logger 

When you have a logger initalized in python, the contents from the `print` do not appear on the log file. Sometimes you may require to also capture the content from `print`. Its quite easy to do this in python. 

`print` accepts a keyword argument called `file`. Per Python documentation, **The file argument must be an object with a write(string) method; if it is not present or None, sys.stdout will be used**. By default, print will push the content to stdout. To push the content to logger, all we need to do is to redirect the stdout to the logger. 

```Python
    import sys 
    import logging 

    logger = logging.getLogger()
    sys.stderr.write = logger.error 
    sys.stdout.write = logger.info
```

