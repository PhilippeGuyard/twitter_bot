web: env > .env; env PYTHONUNBUFFERED=true honcho start -f Procfile.web 2>&1
worker: env > .env; env PYTHONUNBUFFERED=true honcho start -f Procfile.celery 2>&1