# Azure AI Foundry Workshop

<div align="center">

[📦Prerequisites](#-prerequisites) | [🚀Quick Start](#-quick-start) | [🤖Overview](#-overview) | [📔Workshop Content](#-workshop-content) | [🧩Project Structure](#-project-structure) | [❓Support](#-support) | [🤝Contributing](#-contributing)

</div>


## 🤖 Overview

건강 및 식단 조언과 관련된 재미있는 예제를 통해 Azure AI Foundry를 기반으로 지능형 앱 및 AI 에이전트를 빌드하는 방법을 안내하는 실습 워크샵입니다. 다음의 내용을 포함합니다.
- Azure AI Foundry 기본 사항 알아보기
- 인증 및 프로젝트 구성 설정
- AI 모델 배포 및 테스트
- AI 에이전트 빌드(건강 어드바이저 예제)
- 건강 계산 및 식단 계획 구현
- 에이전트 성능 평가 및 품질 속성 모니터링
- 모든 기능과 디자인 패턴이 통합된 엔드투엔드 AI 네이티브 샘플 앱 배포

> **소요 예상 시간**: 4-5 시간  
> **Focus**: 실습, 대화형 노트북, 실용적인 예제, 엔드투엔드 프로젝트

## 🎥 Workshop Overview Video

워크숍에 대한 포괄적인 이해를 돕기 위해 개요 동영상을 시청하세요:

[![Azure AI Foundry Workshop Overview](https://img.youtube.com/vi/0bGLgmZJ6DE/0.jpg)](https://youtu.be/0bGLgmZJ6DE)

## 📸 Visuals

### Sections Overview
<img src="./3-ai-native-e2e-sample/assets/sections.png" width="70%" />
*Figure 1: Overview of different sections in this workshop.*

### End-to-End Sample UI
<img src="./3-ai-native-e2e-sample/assets/ui.png" width="70%" />
*Figure 2: User interface of the end-to-end AI native sample project.*

---

## 📦 사전 준비 사항    

워크샵을 시작하기 전에 다음이 설치되어 있는지 확인하세요:

- [Python 3.10](https://www.python.org/downloads/) 이상 설치
- [Azure AI Foundry](https://ai.azure.com) 에 대한 액세스 권한이 있는 활성 Azure 구독
- [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli) 설치
- [Git](https://git-scm.com/downloads) 설치
- [VS Code](https://code.visualstudio.com/), [GitHub Codespaces](https://github.com/features/codespaces), 또는 [Jupyter Notebook](https://jupyter.org/install) 환경
- 기본 Python 프로그래밍 지식
- Azure AI Foundry에서 모델 배포 및 [AI Search](https://learn.microsoft.com/en-us/azure/search/search-what-is-azure-search) 연결 구성

---

## 🚀 빠른 시작

1. **리포지토리 복제**:
   ```bash
   git clone https://github.com/Azure/ai-foundry-workshop.git
   cd ai-foundry-workshop
   ```

2. **uv 설치**:
   ```bash
   # Unix/Linux/macOS
   curl -LsSf https://astral.sh/uv/install.sh | sh

   # Windows (PowerShell)
   (Invoke-WebRequest -Uri https://astral.sh/uv/install.ps1 -UseBasicParsing).Content | pwsh
   ```

3. **가상 환경 생성 및 활성화**:
   ```bash
   uv venv
   source .venv/bin/activate  # Windows: .venv\Scripts\activate
   ```

4. **Azure AI Foundry 설정**:

   a. **프로젝트 생성 및 리소스 배포**:
      1. [Azure AI Foundry](https://ai.azure.com)로 이동
      2. AI 파운드리 마법사를 사용하여 새 AI 허브 및 프로젝트 만들기
      3. 필요한 모델 배포:
         - Chat/Completion을 위한 GPT 모델(gpt-4o, gpt-4o-mini) (Agent 노트북에서 발생할 수 있는 이슈를 방지하기 위해 **TPM을 최대**로 설정)
         - 벡터 검색을 위한 임베딩 모델
         - 모델이 `Global-Standard` 또는 `DataZone-Standard` 에 배포되었는지 확인하세요.
      4. 연결을 설정합니다:
         - [Grounding with Bing](https://learn.microsoft.com/en-us/azure/ai-services/agents/how-to/tools/bing-grounding?view=azure-python-preview&tabs=python&pivots=overview) 연결 설정
         - Azure AI 검색 연결 구성
      5. Azure AI 파운드리 관리 포털에서 `Azure AI Developer` 역할에 사용자 계정을 추가합니다.

   b. **환경 변수 구성**:
      ```bash
      cp .env.example .env
      ```
      Azure AI Foundry 값으로 `.env`을 업데이트합니다:
      - `PROJECT_CONNECTION_STRING`: Azure ML 워크스페이스의 프로젝트 연결 문자열
      - `MODEL_DEPLOYMENT_NAME`: 모델 배포 이름
      - `EMBEDDING_MODEL_DEPLOYMENT_NAME`: 임베딩 모델 배포 이름
      - `TENANT_ID`: Azure 포털에서 확인한 테넌트 ID
      - `BING_CONNECTION_NAME`: Bing 검색 연결 이름
      - `SERVERLESS_MODEL_NAME`: 서버리스 모델 이름

      > **Note**: `MODEL_DEPLOYMENT_NAME`에 지정된 모델은 Azure AI 에이전트 서비스 또는 어시스턴트 API에서 지원해야 합니다. 자세한 내용은 [지원되는 모델](https://learn.microsoft.com/en-us/azure/ai-services/agents/concepts/model-region-support?tabs=python#azure-openai-models)을 참조하세요. Bing Search를 사용한 그라운딩의 경우 `gpt-4o-mini` 모델을 사용해야 합니다.

5. **종속 요소 설치**:
   ```bash
   # Core Azure AI SDK 및 Jupyter 필요 사항 설치
   uv pip install azure-identity azure-ai-projects azure-ai-inference[opentelemetry] azure-search-documents azure-ai-evaluation azure-monitor-opentelemetry

   # Jupyter 필요사항 설치
   uv pip install ipykernel jupyterlab notebook

   # Jupyter에 커널 등록하기
   python -m ipykernel install --user --name=.venv --display-name="Python (.venv)"

   # 추가 필요사항 설치 (optional - for deploying repo or running mkdocs)
   uv pip install -r requirements.txt
   ```

   > **Note**: VS Code에서 커널 오류가 발생하는 경우, 다음을 시도해보자:
   > 1. 커널을 선택합니다: "Select Kernel" > "Python Environments" > "Python (.venv)" 을 클릭합니다.
   > 2. 커널이 목록에 없으면, `python -m ipykernel install --user --name=.venv` 을 다시 실행하거나, VS Code의 “새 커널 만들기” 마법사를 사용하여 새 Python 환경을 생성합니다.
   > 3. 필요한 경우 VS Code를 다시 로드합니다.

6. **노트북 환경 선택**:

   **옵션 A: VS Code**
   - [VS Code Python extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 설치
   - 둘 중 하나를 설치합니다.:
     - .ipynb 파일용 [Jupyter extension](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter) 
     - .dib 파일용 [Polyglot Notebooks extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.dotnet-interactive-vscode) 
   - 아무 notebook이나 열고 Python 커널(.venv)을 선택합니다.

   **옵션 B: GitHub Codespaces**
   - 리포지토리에서 "Code" > "Create codespace" 을 클릭합니다.
   - 환경이 설정될 때까지 기다립니다.
   - Notebooks를 실행할 준비가 됩니다.

   **옵션 C: Jupyter Lab/Notebook**
   ```bash
   # Install Jupyter if you haven't already
   uv pip install jupyterlab notebook

   # Start Jupyter Lab (recommended)
   jupyter lab

   # Or start Jupyter Notebook
   jupyter notebook
   ```

7. **다음 과정을 차례대로 수행하세요**:
    1. **시작하기** (`1-introduction/`)
       - `1-authentication.ipynb`: SAzure 자격증명 설정
       - `2-environment_setup.ipynb`: 환경 구성
       - `3-quick_start.ipynb`: 기본 작업 알아보기
         
    2. **메인 워크샵** (`2-notebooks/`)
       - 채팅 완료 및 RAG (`1-chat_completion/`)
       - 에이전트 개발 (`2-agent_service/`)
       - Quality Attributes (`3-quality_attributes/`)

---

## 📔 워크샵 학습 경로

워크샵을 완료하려면 이 노트북을 순서대로 따라하세요:

### 1. 시작하기 (`1-introduction/`)
| Notebook | Description |
|----------|-------------|
| [1. Authentication](1-introduction/1-authentication.ipynb) | Azure 자격 증명 및 액세스 설정 |
| [2. Environment Setup](1-introduction/2-environment_setup.ipynb) | 개발 환경 구성 |
| [3. Quick Start](1-introduction/3-quick_start.ipynb) | 기본 Azure AI Foundry 작업 알아보기 |

### 2. 메인 워크샵 (`2-notebooks/`)
| Topic | Notebooks |
|-------|-----------|
| **채팅 완료 및 RAG** | • [Chat Completion & RaG](2-notebooks/1-chat_completion/) |
| **에이전트 개발** | • [Agent Development](2-notebooks/2-agent_service/) |
| **Quality Attributes** | • [Observability & Evaluations](2-notebooks/3-quality_attributes/) |

---

## 🤝 Contributing

We welcome contributions! Please see our [Contributing Guide](CONTRIBUTING.md) for details on how to:
- Submit bug reports and feature requests
- Submit pull requests
- Follow our coding standards
- Participate in code reviews

---

## ❓ Support

If you need help or have questions:

---




