Django-Celery
=============

APScheduler and Django Celery Setup

-----------------------------------

**APScheduler**

    >>> from apscheduler.scheduler import Scheduler
    >>> sc=Scheduler()
    >>> sc.start()
    >>> def job_function():
    ...   print 'hello'
    ... 
    >>> sc.add_cron_job(job_function,month='7',day='24',hour='10',minute=50)


CELERY DOCS
-----------

**install RabbitMQ**

    sudo apt-get install rabbitmq-server

When the command completes the broker is already running in the background, ready to move messages for you: Starting rabbitmq-server: SUCCESS.

**start and stop rabbitmq**

    `sudo invoke-rc.d rabbitmq-server start` and `sudo invoke-rc.d rabbitmq-server stop`

**install django-celery**

    pip install django-celery

**edit setting.py**

    
    import djcelery

    djcelery.setup_loader()

    BROKER_URL = 'amqp://guest:guest@localhost:5672/'

add it to installed_apps ie `'djcelery'`,


**create a new app:**

    django-admin.py startapp tt

add it to installed_apps


**add tasks.py file to app tt**


    import celery
    @celery.task()
    def add(x, y):
        return x + y

    import datetime
    @celery.decorators.periodic_task(run_every=datetime.timedelta(seconds=5))
    def my_task():
        # Insert fun-stuff here
        x=wells().save()
        print ('good 1minutes passed.')
        return 0


to work *periodic_task* you must run `python manage.py celery beat` or `python manage.py celeryd -B -l DEBUG`

to work *task* you must run `python manage.py celery worker --loglevel=info`


**testing**

    >>> from tt import tasks
    #always a shortcut to .apply_async
    >>> tasks.add.delay(3,4)
    #executes 10 seconds from now.
    >>> tasks.add.apply_async((3,4),countdown=10)
    #executes 10 seconds from now, specifed using eta
    >>> tasks.add.apply_async(eta=now + timedelta(seconds=10))


**Running the worker as a daemon**

**install init.d script: celeryd**

 - copy the file celeryd(https://github.com/celery/celery/blob/3.0/extra/generic-init.d/celeryd) and pasted it in folder `/etc/init.d/`

 - created a configuration file celeryd in folder `/etc/default/`


config file (celeryd) in folder `/etc/default`


    CELERYD_NODES="w1"
    # or we could have three nodes:
    #CELERYD_NODES="w1 w2 w3"
    
    # Where to chdir at start.
    CELERYD_CHDIR="/home/suhail/Desktop/projects/celeryeg2"
    
    
    # Python interpreter from environment.
    ENV_PYTHON="/home/suhail/Envs/celeryenv/bin/python"
    
    # How to call "manage.py celeryd_multi"
    CELERYD_MULTI="$ENV_PYTHON $CELERYD_CHDIR/manage.py celeryd_multi"
    
    # How to call "manage.py celeryctl"
    CELERYCTL="$ENV_PYTHON $CELERYD_CHDIR/manage.py celeryctl"
    
    # Extra arguments to celeryd
    CELERYD_OPTS="--beat --time-limit=300 --concurrency=8"
    
    # %n will be replaced with the nodename.
    CELERYD_LOG_FILE="$CELERYD_CHDIR/%n.log"
    CELERYD_PID_FILE="$CELERYD_CHDIR/%n.pid"
    
    # Workers should run as an unprivileged user.
    #CELERYD_USER="celery"
    #CELERYD_GROUP="celery"
    
    # Name of the projects settings module.
    export DJANGO_SETTINGS_MODULE="celeryeg2.settings"



 - to start run `sudo /etc/init.d/celeryd start`
 - to stop run `sudo /etc/init.d/celeryd stop`
 - more CELERYD_OPTS (http://docs.celeryproject.org/en/latest/reference/celery.bin.celeryd.html)