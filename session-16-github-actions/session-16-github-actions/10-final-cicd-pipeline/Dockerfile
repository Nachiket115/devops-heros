# Multi-stage lightweight Dockerfile for Python Calculator App
FROM python:3.12-slim AS builder

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

FROM python:3.12-slim AS runner

WORKDIR /app

# Copy installed python dependencies from builder
COPY --from=builder /root/.local /root/.local
ENV PATH=/root/.local/bin:$PATH

# Copy application code
COPY app/ /app/app/

# Environment configuration
ENV PYTHONUNBUFFERED=1

CMD ["python", "app/calculator.py"]
