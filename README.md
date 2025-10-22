# Data Science Project Template

This repo is inspired by the [Docker for Datascience book](https://www.amazon.com/Docker-Data-Science-Extensible-Infrastructure/dp/1484230116). It's a Docker image with a data science environment based on the [jupyter/datascience-notebook](https://hub.docker.com/r/jupyter/datascience-notebook/) with pandas, matplotlib, scipy, seaborn and scikit-learn pre-installed.

Готовая среда для быстрого старта Data Science проектов с использованием Docker, Jupyter Lab и PostgreSQL.

## To start new Data Science project (Быстрый старт)

### 1. Клонируйте репозиторий

Clone the repository:
Create a new directory, cd into it, and then run

```
git clone https://github.com/vulcan4ik/data-science-project-template.git
cd data-science-project-template
```

Or you can just download it as a zip and use it without git.

### 2. Add your favorite Python modules to ./docker/jupyter/requirements.txt. For example:

```
xgboost
tensorflow==1.6.0
```

Or use pip install right in jupyter (don't forget ! in front of the command)

```
!pip install your_package
```

### 3. Start containers(Запустите контейнеры)

```
docker-compose up
```
При первом запуске Docker скачает образы и установит все библиотеки (может занять 5-10 минут).
### 4. Open Jupyter Lab
Copy a jupyter url from terminal and open it in your browser.
 Open Jupyter Lab:
http://localhost:8888
### 5. Create your notebooks

Find an `examples.ipynb` notebook in ipynb folder. Create your own notebooks.

### 6. Work with data

Copy your data into `./data` and read it in Jupyter. You can also upload data into PostgreSQL, which is running in its own container along with Jupyter (see examples notebook for details).

Всё, что вы создаёте в Jupyter внутри папки /home/jovyan/work, автоматически сохраняется на вашем компьютере в папке проекта.

### 7. Stop containers

```
docker-compose down
```

8. Update images
```
docker-compose build --pull
```

9. Clean Docker's mess

```
docker rmi -f $(docker images -qf dangling=true)
```

Sometimes it is useful to remove all docker's data.

```
docker system prune
```

## 🗄️ Работа с PostgreSQL

### Подключение из Jupyter
#### Через psycopg2
```
import psycopg2 as pg2
from sqlalchemy import create_engine, text
```

```
con = pg2.connect(
    host='this_postgres',
    user='postgres',
    password='password',
    database='postgres')
```

#### Через SQLAlchemy

```
engine = create_engine('postgresql://postgres:password@this_postgres:5432/postgres')
```    

## 🛠️ Полезные команды

### Остановить контейнеры

`docker-compose down`



### Остановить и удалить базу данных

`docker-compose down -v`


### Пересобрать образы после изменения requirements.txt
```
docker-compose build --no-cache
docker-compose up
```

### Просмотр логов

docker-compose logs jupyter
docker-compose logs postgres

text

### Добавление новых библиотек

1. Отредактируйте `docker/jupyter/requirements.txt`
2. Пересоберите образ:
```
docker-compose down
docker-compose build
docker-compose up
```


## 🔧 Кастомизация

### Изменение порта Jupyter

В `docker-compose.yml`:
ports:

"9999:8888" # Теперь доступен на http://localhost:9999



### Добавление токена безопасности

В `docker-compose.yml` измените команду:
`command: start-notebook.py --ServerApp.token='your-secret-token'`


### Настройка PostgreSQL

Измените переменные окружения в `docker-compose.yml`:
environment:

POSTGRES_PASSWORD: your_password
POSTGRES_USER: your_user
POSTGRES_DB: your_database

##  License

This project is free to use for educational purposes.