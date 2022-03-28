web: gunicorn config.wsgi:application
worker: celery -A config.celery_app worker --loglevel=info --beat
