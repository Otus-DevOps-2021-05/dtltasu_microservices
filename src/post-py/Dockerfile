FROM python:3.6.0-alpine
WORKDIR /app
COPY . /app
RUN apk add --no-cache build-base && \
    pip install --upgrade pip && \
    pip install "markupsafe==1.1.1" && \
    pip install -r requirements.txt
ENV POST_DATABASE_HOST post_db
ENV POST_DATABASE posts
CMD ["python3", "post_app.py"]
