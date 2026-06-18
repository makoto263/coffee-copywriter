# coffee-copywriter(Gemma-2-2B-LoRA)

## 1. 프로젝트 소개 (Project Introduction)
본 프로젝트는 SNS 사용자들의 일상적인 감정 상태(Text)를 분석하여, 해당 분위기와 기분에 완벽하게 매칭되는 **캔커피 맞춤형 광고 캐치프레이즈(Tagline)**를 자동 생성하는 페르소나 특화 LLM입니다. 일상에 지친 사용자에게 위로와 활력을 주는 '감성형 카피라이터'로서의 페르소나를 구축하였습니다.

---

## 2. 사용 기술 스택 (Tech Stack)
* **Base Model**: `google/gemma-2-2b-it` (Unsloth 4-bit 量子化版)
* **Framework**: `Unsloth` (Fast 16-bit/4-bit training)
* **Method**: `LoRA / QLoRA` (PEFT)
* **Infrastructure**: Google Colab (Tesla T4 GPU)
* **Local Run Environment**: Ollama (Mac/Windows Local)

---

## 3. 파인튜닝 설계 설명 (Fine-Tuning Design)
* **모델 선택 근거**: 제한된 자원(Colab T4 GPU) 및 로컬 환경(Ollama)에서의 원활한 실행을 위해 3B 이하의 경량 모델 중 가장 뛰어난 언어 이해도를 가진 `Gemma-2-2B-it`을 채택했습니다.
* **데이터셋 설계**: 사용자의 감정이 담긴 SNS 포스트 데이터와 이에 대응하는 Gemini 기반의 고품질 광고 카피 정답 쌍을 구축하여 응답 부분(`train_on_responses_only`)만 집중 학습시켰습니다.
* **하이퍼파라미터 설정 근거**:
  * `Epochs`: 3 (과적합 곡선 분석을 위한 충분한 학습량 설정)
  * `Learning Rate`: 2e-4 (Unsloth 공식 권장 및 LoRA 학습 안정성을 위한 표준 값)
  * `r=16, lora_alpha=16`: 모델의 표현력을 유지하면서 메모리 효율을 극대화하는 파라미터 튜닝.

---

## 4. 설치 및 실행 방법 (Installation & Usage)

### 1) Colab / 학습 및 추론 절차
1. 본 리ポジトリ의 `.ipynb` 파일을 Google Colab에 업로드합니다.
2. 의존성 라이브러리(`unsloth`, `transformers`, `trl`)를 설치합니다.
3. 데이터셋을 로드하고 파인튜닝 코드를 순차적으로 실행합니다.

### 2) 로컬 환경 (Ollama) 실행 방법
1. 에스포트된 `gemma2-2b-it.Q4_K_M.gguf` 파일과 `Modelfile`을 동일한 디렉토리에 위치시킵니다.
2. 터미널을 열고 해당 폴더로 이동 후 아래 명령어를 실행하여 모델을 생성합니다:
```bash
   ollama create coffee-ai -f ./Modelfile
