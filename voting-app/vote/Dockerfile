FROM python:3.11-slim AS builder
WORKDIR /app/vote
COPY vote/requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

FROM python:3.11-slim
WORKDIR /app/vote
COPY --from=builder /usr/local/lib/python3.11/site-packages /usr/local/lib/python3.11/site-packages

COPY vote/ .

EXPOSE 8080
CMD ["python", "app.py"]