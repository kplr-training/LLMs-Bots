FROM python:3.12
WORKDIR /app
COPY . /app
RUN pip install -r requirements.txt
EXPOSE 8503
CMD ["streamlit", "run", "main.py", "--server.port=8503"]
