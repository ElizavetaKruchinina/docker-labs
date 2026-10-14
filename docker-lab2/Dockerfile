FROM python:3.12-alpine AS builder

WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

FROM python:3.12-alpine

WORKDIR /app

RUN addgroup -S appuser && adduser -S appuser -G appuser

COPY --from=builder /usr/local/lib/python3.12/site-packages/ /usr/local/lib/python3.12/site-packages/
COPY --from=builder /usr/local/bin/ /usr/local/bin/
COPY app/ ./app/

RUN chown -R appuser:appuser /app

ENV PORT=8000
ENV ROCKET_SIZE=Small

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD python -c "import urllib.request; urllib.request.urlopen('http://localhost:8000/health')"

ARG LAB_LOGIN
ARG LAB_TOKEN
LABEL org.lab.login=$LAB_LOGIN \
      org.lab.token=$LAB_TOKEN

USER appuser

ENTRYPOINT ["gunicorn", "--bind", "0.0.0.0:8000", "--workers", "2","--worker-tmp-dir", "/dev/shm", "app.app:app"]
