# Chatbot using Mistral :

![image](https://github.com/kplr-training/LLMs-Bots/assets/123749462/09abd3ef-94a3-47eb-b1ea-548f8bfca7ab)


## Setup with Docker :

- Build the Docker Image:
```
docker buildx build --platform=linux/amd64 -t local-llm:v1 .
```
This command uses the docker buildx extension to build a Docker image (local-llm:v1) from the current directory (.) for the linux/amd64 platform.

- Run the Docker Container:
```
docker run -p 8503:8503 local-llm:v1
```
This command runs a Docker container from the local-llm:v1 image, and it maps port 8503 from the host to port 8503 in the container (-p 8503:8503).

## Setup without Docker

### Prerequisites

To run this code, make sure you have the following prerequisites installed:

- Python version 3.11

- Follow the steps below to set up and run the code:

1. Clone the repository to your local machine.

2. Create a virtual environment using the following commands:

```bash
python -m venv venv
```
3. Activate the virtual environment. In PowerShell, use:

```bash
.\venv\Scripts\Activate
```
4. Upgrade pip to the latest version:

```bash
python -m pip install --upgrade pip
```

5. Install the required dependencies from the requirements.txt file:

```bash
pip install -r requirements.txt
```
## Running the Application

- After completing the setup, run the following command to start the application:

```bash
streamlit run main.py
```


