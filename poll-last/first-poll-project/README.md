- Install python3-pip on server.

```bash
sudo apt install python3-pip
pip3 --version
```
- Install django.

```bash
# pip install Django==4.1.6
python3 -m pip install Django
python3 -m django --version
```

- Run manage.py
```bash
python3 manage.py migrate  # what is migrate ?
python3 manage.py runserver
```

- install python-decouple (4.5 video)

```bash
pip3 install python-decouple
```

- Copy `from decouple import config` in polling/settings.py

- Create `.env` file in poll-app
    - in `settings.py` `SECRET_KEY` ENVIRONMENT OLARAK AL ve `.env` icine yapistir. `SECRET_KEY=iql)an966qsxsg&8onv&^s4k3-g&4i(0+ddu491za5r!%%!*fi`
    - settings.py deki `SECRET_KEY` degerini `SECRET_KEY = config('SECRET_KEY') ` OLACAK sekilde degistir.
    - DEBUG  yerine `DEBUG = config('DEBUG', default=False, cast=bool)` bunu koplaya
    - .env icnede `DEBUG=True` kopyala
    - settings.py de `ALLOWED_HOSTS = config('ALLOWED_HOSTS', cast=Csv())` kopyala
    - settings.py de `from decouple import Csv` kopyala
    - settings.py de `database` kismini fotodaki gibi configure et
     `TIME_ZONE = config('TIME_ZONE')`
    - 

- /admin kismindaki username ve password icin once `python3 manage.py createsuperuser` kodu gir


## pushing the app code

- firstly, create a `.gitignore` 
- Copy the following in the `.gitignore`
```txt
__pycache__/

.env

*.sqlite3
```
- Then go to the `gitlab` and Click `New project`
    ```txt
    - Give name in project name
    - Visibility Level is private
    - Click `Create project`
    ```
- Copy the following into the terminal

```sh
cd /poll-app
git init
git add .
git commit -m "added poll-app project"
git remote add origin git@<gitlab>
git remote -v
git push -u origin master
```
- After control in gitlab.

- Create `ssh-keygen` in the app-server.
    - Then copy `id_rsa.pub` in gitlab SSH-Key.

## Install Postgresql in app-server
```bash
sudo apt update
sudo apt install python3-venv python3-dev libpq-dev postgresql postgresql-contrib nginx curl
sudo -u postgres psql
```
```sh
postgres=# CREATE DATABASE pollingdb;
postgres=# \l
postgres=# CREATE USER pollinguser WITH PASSWORD '12345';
postgres=# \du
```
- Configure
```bash
ALTER ROLE pollinguser SET client_encoding TO 'utf8';
ALTER ROLE pollinguser SET default_transaction_isolation TO 'read committed';
ALTER ROLE pollinguser SET timezone TO 'UTC';
GRANT ALL PRIVILEGES ON DATABASE pollingdb TO pollinguser;
```
- Exit in postgres `\q`

## Creating a Python Virtual Environment for your Project
```bash
sudo apt install python3-virtualenv
virtualenv venv
source venv/bin/activate
pip install django gunicorn psycopg2-binary pillow
python3 -m pip install --upgrade pip
```
## 6. Local DNS on Production Server
- Ping atabilmesi icin makinenin `private ip` addresini girmek gerekiyor.
```bash
sudo nano /etc/hosts
```
- BUradan ip adresini tanitabilirsin.

## 7. Deploying Our Application Code to App Server
- Copy the following in the settings.py and control that there is `import os`.
```bash
STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
```
## 8. Configure the Application Settings file File on the App Server
```bash
cp .env.example .env
nano .env
```
- Install requirements.txt
```sh
pip install -r requirements.txt
python3 manage.py migrate
```
- if you have a failed about AutoField, config in `settings.py`
```bash
DEFAULT_AUTO_FIELD='django.db.models.AutoField'
```
- you can migrate the initial database schema to our PostgreSQL database using the management script
- Create an administrative user for the project by typing
```bash
python3 manage.py migrate
python3 manage.py createsuperuser
```
```txt
Username= said
password= 12345
```
- You can collect all of the static content into the directory location that you configured by typing
```bash
python3 manage.py collectstatic
```

- Finally, you can test out your project by starting up the Django development server with this command
```bash
python3 manage.py runserver 0.0.0.0:8000
```
- In your web browser, visit your server’s domain name or IP address followed by :8000

### Testing Gunicorn’s Ability to Serve the Project
```bash
cd /first-poll-project
gunicorn --bind 0.0.0.0:8000 polling.wsgi:application
```
- This will start Gunicorn on the same interface that the Django development server was running on. You can go back and test the app again in your browser.
- When you are finished testing, hit CTRL-C in the terminal window to stop Gunicorn.
    - You’re now finished configuring your Django application. You can back out of our virtual environment by typing
    ```bash
    deactiate
    ```
- Start by creating and opening a systemd socket file for Gunicorn with sudo privileges `sudo nano /etc/systemd/system/gunicorn.socket`
    - Copy the following
    ```bash
    [Unit]
    Description=gunicorn socket

    [Socket]
    ListenStream=/run/gunicorn.sock

    [Install]
    WantedBy=sockets.target
    ```
- `sudo nano /etc/systemd/system/gunicorn.service`
```bash
[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target

[Service]
User=ubuntu
Group=www-data
WorkingDirectory=/home/ubuntu/first-poll-project/polling
ExecStart=/home/ubuntu/venv/bin/gunicorn \
          --access-logfile - \
          --workers 3 \
          --bind unix:/run/gunicorn.sock \
          polling.wsgi:application

[Install]
WantedBy=multi-user.target
```


