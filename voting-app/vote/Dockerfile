FROM python:3.13-slim AS builder
WORKDIR /app/vote
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
FROM python:3.13-slim
WORKDIR /app/vote
COPY --from=builder /usr/local/lib/python3.13/site-packages /usr/local/lib/python3.13/site-packages
COPY . .
EXPOSE 5000
CMD ["python", "app.py"]