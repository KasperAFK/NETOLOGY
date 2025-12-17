# ДЗ по теме "Практическое применение Docker"

## Задание 0

![](./image/DZ_555_1.png)

## Задание 1

Dockerfile.python
```
FROM python:3.12-slim

WORKDIR /app

COPY . .

RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir fastapi uvicorn mysql-connector-python

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5000"]
```

.dockerignore
```
.git
.gitignore

__pycache__/
*.pyc
*.pyo
*.pyd

.venv/
venv/
env/

.idea/
.vscode/

*.log
.DS_Store

```

![](./image/DZ_555_2.png)

## Задание 2

