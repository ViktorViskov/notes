```python
from apscheduler.schedulers.background import BackgroundScheduler
from apscheduler.triggers.cron import CronTrigger


def daily_task_example():
    pass

def start():
    scheduler = BackgroundScheduler()
    trigger = CronTrigger(hour=1, minute=0, second=0, timezone='UTC')
    scheduler.add_job(daily_task_example, trigger)
    scheduler.start()
```
[[schedule]]
[[background]]
[[python]]