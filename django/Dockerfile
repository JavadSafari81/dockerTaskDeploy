FROM python:3.9.20-alpine3.20

WORKDIR /app

COPY requirements.txt requirements.txt

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

ENV PATH="/mysite/venv/bin:$PATH"

CMD ["python", "./mysite/djangoProject/manage.py", "runserver", "0.0.0.0:8000"]
