aws steps to deployment django:


  sudo apt-get update
  sudo apt-get install python-pip python-dev libpq-dev postgresql postgresql-contrib nginx

configure postgres:

  sudo -u postgres psql

  create database myproject;
  create user myprojectuser with password 'password';
  alter role myprojectuser set client_encoding to 'utf8';
  alter role myprojectuser set default_transaction_isolation to 'read committed';
  alter role myprojectuser set timezone to 'UTC';
  grant all privileges on database myproject to myprojectuser;


configure pip:

  sudo -H pip install --upgrade pip
  sudo -H pip install virtualenv
  create virtualenv
  pip install django gunicorn psycopg2
  pip install psycopg2-binary

create django-project:

  django-admin startproject myproject ~/myproject

open settings.py

DATABASES = {
  'default': {
      'ENGINE': 'django.db.backends.postgresql_psycopg2',
      'NAME': 'myproject',
      'USER': 'myprojectuser',
      'PASSWORD': 'password',
      'HOST': 'localhost',
      'PORT': ''
  }
}


  STATIC_ROOT = os.path.join(BASE_DIR, 'static/')


  python manage.py makemigrations
  python manage.py migrate
  sudo ufw allow 8000
  python manage.py runserver 0.0.0.0:8000
  or
  gunicorn --bind 0.0.0.0:8000 myproject.wsgi

  None: change wizards - inbound in aws
  and make all traffic and 0.0.0.0/0
  visit: http://18.221.162.213:8000/ (don't forget for port no)


  deactivate virtualenv
  sudo nano /etc/systemd/system/gunicorn.service

Add these inside:

[Unit]
Description=gunicorn daemon
After=network.target

[Service]
User=ubuntu
Group=www-data
WorkingDirectory=/home/ubuntu/myproject
ExecStart=/home/ubuntu/myproject/env/bin/gunicorn --access-logfile - --workers 3 --bind unix:/home/ubuntu/myproject/myproject.sock myproject.wsgi:application

[Install]
WantedBy=multi-user.target

  sudo systemctl daemon-reload
  sudo systemctl start gunicorn
  sudo systemctl enable gunicorn

  sudo journalctl -u gunicorn
  sudo systemctl daemon-reload
