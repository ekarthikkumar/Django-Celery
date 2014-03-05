Django-Celery
=============

APScheduler and Django Celery Setup

-----------------------------------

    >>> from apscheduler.scheduler import Scheduler
    >>> sc=Scheduler()
    >>> sc.start()
    >>> def job_function():
    ...   print 'hello'
    ... 
    >>> sc.add_cron_job(job_function,month='7',day='24',hour='10',minute=50)