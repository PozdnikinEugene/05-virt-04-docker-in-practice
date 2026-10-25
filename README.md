# Домашнее задание к занятию "`Название занятия`" - `Фамилия и имя студента`


### Инструкция по выполнению домашнего задания

   1. Сделайте `fork` данного репозитория к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/git-hw или  https://github.com/имя-вашего-репозитория/7-1-ansible-hw).
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. В личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!
   
### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

---

### Задание 0
Проверяю нужные версии Docker - `docker version` и `docker compose version`
![0](img/0-1.png)
### Задание 1

Содержимое [Dockerfile.python](https://github.com/PozdnikinEugene/05-virt-04-docker-in-practice-fork/blob/main/Dockerfile.python)


```
# Stage 1: Build stage
FROM python:3.12-slim AS builder
WORKDIR /app
COPY requirements.txt .
# Создаем виртуальное окружение и устанавливаем зависимости туда
RUN python -m venv /opt/venv && \
    /opt/venv/bin/pip install --no-cache-dir -r requirements.txt

# Stage 2: Final stage
FROM python:3.12-slim
WORKDIR /app
# Копируем созданное виртуальное окружение целиком
COPY --from=builder /opt/venv /opt/venv
COPY . .
# Добавляем путь к бинарникам venv в PATH, чтобы uvicorn вызывался напрямую
ENV PATH="/opt/venv/bin:$PATH"
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]

Файл [.dockerignore](https://github.com/PozdnikinEugene/05-virt-04-docker-in-practice-fork/blob/main/.dockerignore)
Файл [.gitignore](https://github.com/PozdnikinEugene/05-virt-04-docker-in-practice-fork/blob/main/.gitignore)

```
Сборка контейнера

```
docker build -f Dockerfile.python -t my-python-app:latest .
```
Запуск

```
docker run -d -p 5000:5000 --name my-python-container my-python-app:latest
```
Проверка 
```
docker logs my-python-container
```
Вывод консоли

![1](img/1-1.png)




---

### Задание 3
Файл [compose.yaml](https://github.com/PozdnikinEugene/05-virt-04-docker-in-practice-fork/blob/main/compose.yaml)
Запускаю проект 

```
docker compose up -d
```

Делаю 

```
curl -L http://127.0.0.1:8090
```
Проект поднялся и отвечает на запрос

![1](img/3-1.png)

Захожу в контейнер DB. Для безопасности пароль не ввожу и оставляю пустое `-p` для того что бы он его запросил 

```
docker exec -ti 05-virt-04-docker-in-practice-fork-db-1 mysql -uroot -p
```
Выполняю требуемые запросы 

```
mysql> show databases;
mysql> use virtd;
mysql> show tables; SELECT * from requests LIMIT 10;
```

Останавливаю проект

```
docker compose down
```
Вывод консоли

![1](img/3-2.png)

---

### Задание 4
Создаю ВМ в YC
![4-1](img/4-1.png)

Запускаю скрипт 

```
./app.sh
```
Проверяю на сервере
```
curl -L http://127.0.0.1:8090
```
Вывод консоли
![4-2](img/4-2.png)

Захожу на сайт проверки `https://check-host.net/check-http` b и указываю свой адрес `http://178.154.249.231:8090/`
![4-3](img/4-3.png)

Проверяю содержимое базы

```
docker exec -ti app-db-1 sh -c 'mysql -uroot -p"$MYSQL_ROOT_PASSWORD" -e "USE virtd; SELECT * FROM requests LIMIT 10;"'
```
![4-4](img/4-4.png)

[Ссылка на fork](https://github.com/PozdnikinEugene/05-virt-04-docker-in-practice-fork.git)

### Задание 4
