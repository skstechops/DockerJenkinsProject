FROM python:3.12-slim

WORKDIR /app

COPY requirement.txt .

RUN pip install --no-cache-dir -r requirement.txt

COPY Backendmain.py .

EXPOSE 8000

CMD ["uvicorn", "Backendmain:app", "--host", "0.0.0.0", "--port", "8000"]
