# coffee-copywriter(Gemma-2-2B-LoRA)

## 1. 프로젝트 소개 (Project Introduction)
본 프로젝트는 SNS 사용자들이 게시물을 분석하여, 해당 분위기와 기분에 완벽하게 매칭되는 '캔커피 맞춤형 광고 copy'를 자동 생성하는 페르소나 특화 LLM이다. 현대 광고 특성으로 맞춤형 아르고리즘을 사용하고 SNS 사용자 한명 한명의 맞춘 삼품을 추전하고 있다. 하지만 그 광고의 copy까지는 SNS 사용자의 상황이나 감정의 맞줘 바뀌지는 않다. 예를 들으면 지금 축구 월드컵을 하고 있다. 공식 스폰서의 Coca Cola는  SNS에서 '공식 스폰서 코카-콜라와 함께 이번 26 FIFA 월드컵의 모든 순간들을 온몸으로 느껴보세요!' 라는 광고를 하고 있다. 하지만 밤늦게 시합을 보고 수면 부족이라고 SNS에서 투고하는 사람과 자기 나라가 이겨 기뽀하는 사람에 같은 광고를 해도 효과가 절반으로 줄어든다고 생각한다. 전자에는 '늦은 밤, 온몸을 던진 응원의 열기. 아침의 피로는 톡 쏘는 짜릿함으로 깨우세요! 코카-콜라와 함께 다음 승리까지 승부하기' 후자에는 '이 역사적인 승리의 순간, 세상에서 가장 짜릿한 코카-콜라로 다 함께 건배!'와 같은 copy를 사용하는게 더 구매 의욕을 자극할 것이다. 

---

## 2. 사용 기술 스택 (Tech Stack)
* **Base Model**: `google/gemma-2-2b-it` (Unsloth 4-bit)
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
