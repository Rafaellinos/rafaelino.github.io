---
title: Como remover style/cores do json logger
author: Rafael Lino
categories: [Python]
tags: [python]
---

<p>Ao utilizar a lib python python-json-logger==2.0.1, me deparei com o 
problema em como remover as cores dos logs, uma vez que "bagunçavam" os logs
com código como "\x1b[31;20m", que basicamente representam alguma cor, não
fazendo sentido para o json-logger.</p>

<h3>Examplo:</h3>

```python
from datetime import datetime

import click
from flask import request
from pythonjsonlogger import jsonlogger

class CustomJsonFormatter(jsonlogger.JsonFormatter):
    def add_fields(self, log_record, record, message_dict):

        if isinstance(record.msg, str): # remove msg style
            record.msg = click.unstyle(record.msg)
        if isinstance(record.message, str): # remove msg style
            record.message = click.unstyle(record.message)

        super().add_fields(log_record, record, message_dict)

        log_record["timestamp"] = datetime.now().strftime("%Y-%m-%dT%H:%M:%S.%fZ")
        if request:
            log_record["path"] = request.path
            log_record["method"] = request.method
```

