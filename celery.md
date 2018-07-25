h1 celery
====
Ставим селери
`pip install celery==4.0.2`
создаем в главном апп проекта файл celery.py и пишем туда
```
from __future__ import absolute_import, unicode_literals
import os
from celery import Celery


os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'proj.settings')

app = Celery('proj')
app.config_from_object('django.conf:settings')
app.autodiscover_tasks()
```
Вместо proj естественно название главного аппа


В settings.py прописываем это
```
CELERY_BROKER_URL = 'redis://localhost:6379'
CELERY_RESULT_BACKEND = 'redis://localhost:6379'
CELERY_ACCEPT_CONTENT = ['application/json']
CELERY_RESULT_SERIALIZER = 'json'
CELERY_TASK_SERIALIZER = 'json'
CELERY_TIMEZONE = 'Asia/Makassar'
CELERY_BEAT_SCHEDULE = {}
```
В главном app в __init__ прописываем это
```
from __future__ import absolute_import, unicode_literals


from .celery import app as celery_app

__all__ = ['celery_app']
```
Чтобы добавлять периодические таски пропишем в settings.py
```
from celery.schedules import crontab
CELERY_BEAT_SCHEDULE = {
    'task-number-one': {
        'task': 'app1.tasks.task_number_one',
        'schedule': crontab(minute=59, hour=23),
        'args': (*args)
    },
    'task-number-two': {
        'task': 'app2.tasks.task_number_two',
        'schedule': crontab(minute=0, hour='*/3,10-19'),
        'args': (*args)
    }
}

```
Дальше просто прописываем таски в app!