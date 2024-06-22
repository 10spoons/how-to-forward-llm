# LLM API endpoint 을 포트 포워딩 하여 접속 하기

- vLLM을 설치하고 `Mistral-7B-Instruct-v0.3` 모델을 OpenAI API로 서비스하도록 합니다.
    ```bash
    pip install vllm

    python -u -m vllm.entrypoints.openai.api_server --host 0.0.0.0 --model mistralai/Mistral-7B-Instruct-v0.3
    ```

- ngrok 을 설치하고 ngrok을 가입하여 토큰을 발급 받아 인증을 해준 뒤 vLLM 의 포트(8000)을 외부로 포워딩합니다.
    ```bash
    curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null && echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list && sudo apt update && sudo apt install ngrok
    
    ngrok config add-authtoken 토큰

    ngrok http 8000
    ```

- 외부에서는 아래와 같이 접속하면 됩니다. (짝통 OpenAI API 처럼 사용하는 예시)
```python
from openai import OpenAI

openai_api_key = "EMPTY"
openai_api_base = "<ngrok이 생성한 주소>/v1"
client = OpenAI(
    api_key=openai_api_key,
    base_url=openai_api_base,
)
completion = client.completions.create(model="mistralai/Mistral-7B-Instruct-v0.3",
                                      prompt="San Francisco is a")
print("Completion result:", completion)
```