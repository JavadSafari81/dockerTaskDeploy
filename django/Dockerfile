FROM python:3.9.20-alpine3.20

WORKDIR /app

RUN apk add gcc musl-dev postgresql-dev

COPY requirements.txt requirements.txt

RUN pip install -r requirements.txt

COPY . .

ENV PATH="/mysite/venv/bin:$PATH"

CMD ["python", "./mysite/djangoProject/manage.py", "runserver", "0.0.0.0:8000"]
