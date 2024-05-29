```python
import time

import schedule


# Define a function to schedule the task every hour
def schedule_task():
    pass

if __name__ == "__main__":
    #schedule.every().day.at("00:00").do(schedule_task)
    schedule.every().hour.at(":00").do(schedule_task)

    while True:
        schedule.run_pending()
        time.sleep(1)
```
[[python]]
[[schedule]]