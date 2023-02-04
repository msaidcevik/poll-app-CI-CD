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


# pushing the app code

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

