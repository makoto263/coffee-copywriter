# coffee-copywriter(Gemma-2-2B-LoRA)

## 1. 프로젝트 소개 (Project Introduction)
본 프로젝트는 SNS 사용자들이 게시물을 분석하여, 해당 분위기와 기분에 완벽하게 매칭되는 '캔커피 맞춤형 광고 copy'를 자동 생성하는 페르소나 특화 LLM이다. 현대 광고 특성으로 맞춤형 아르고리즘을 사용하고 SNS 사용자 한명 한명의 맞춘 삼품을 추전하고 있다. 하지만 그 광고의 copy까지는 SNS 사용자의 상황이나 감정의 맞줘 바뀌지는 않다. 
예를 들으면 지금 축구 월드컵을 하고 있다. 공식 스폰서의 Coca Cola는  SNS에서 '공식 스폰서 코카-콜라와 함께 이번 26 FIFA 월드컵의 모든 순간들을 온몸으로 느껴보세요!' 라는 광고를 하고 있다. 하지만 밤늦게 시합을 보고 수면 부족이라고 SNS에서 투고하는 사람과 자기 나라가 이겨 기뽀하는 사람에 같은 광고를 해도 효과가 절반으로 줄어든다고 생각한다. 전자에는 '늦은 밤, 온몸을 던진 응원의 열기. 아침의 피로는 톡 쏘는 짜릿함으로 깨우세요! 코카-콜라와 함께 다음 승리까지 승부하기' 후자에는 '이 역사적인 승리의 순간, 세상에서 가장 짜릿한 코카-콜라로 다 함께 건배!'와 같은 copy를 사용하는게 더 구매 의욕을 자극할 것이다. 

---

## 2. 사용 기술 스택 (Tech Stack)
* **Base Model**: `google/gemma-2-2b-it` (Unsloth 4-bit)
* **Framework**: `Unsloth` (Fast 16-bit/4-bit training)
* **Method**: `LoRA / QLoRA` (PEFT)
* **Infrastructure**: `Google Colab` (Tesla T4 GPU)
* **Local Run Environment**: `Ollama` (Mac/Windows Local)

---

## 3. 파인튜닝 설계 설명 (Fine-Tuning Design)
* **모델 선택 근거**: 제한된 자원(Colab T4 GPU) 및 로컬 환경(Ollama)에서의 원활한 실행을 위해 3B 이하의 경량 모델 중 가장 뛰어난 언어 이해도를 가진 `Gemma-2-2B-it`을 채택했다.
* **데이터셋 설계**: Twitter Emotion Dataset이라는 영어로 Twitter의 투고된 문장과 그 문장에 대한 강정이 레이벌링(sadness (0), joy (1), love (2), anger (3), fear (4),surprise (5)) 된 데이터셋을 사용. 또 그 문장에 대한 맞춤형 copy를 gemini-2.5-flas를 사용하고 작성 했다. 
* **하이퍼파라미터 설정 근거**:
  * `Epochs`: 3 (과적합 곡선 분석을 위한 충분한 학습량 설정)
  * `Learning Rate`: 2e-4 (Unsloth 공식 권장 및 LoRA 학습 안정성을 위한 표준 값)
  * `r=16, lora_alpha=16`: 모델의 표현력을 유지하면서 메모리 효율을 극대화하는 파라미터 튜닝.

---

## 4. 베이스 vs 파인튜닝
* **기본 베이스 모델 (LoRA 미적용)**: 대화형 Instruct 모델의 특성이 남아있어, copy를 직접 출력하기 전에 *"Here are some taglines..."*와 같은 불필요한 서두(Intro)를 출력하거나 글머리 기호(*)를 사용하여 나열하는 경향을 볼 수 있다. 이로 인해 제한된 max_new_tokens(45자) 내에서 정답을 완성하지 못하고 문장이 중간에 끊기는 한계가 명확히 드러난다.
* **파인튜닝 모델 (LoRA 적용)**:서두나 설명문을 완전히 배제하고, 오직 1문장의 완성된 광고 copy만을 다이렉트로 생성하고 있다. 특히 사용자의 감정 상태(공포, 짜증, 분노 등)를 정확히 포착하여 브랜드 메시지와 결부시키는 능력이 압도적으로 향상되었으며, 제한된 토큰 내에서 끊김 없이 완벽한 문장을 종결지음으로써 '카피라이터 페르소나'가 성공적으로 구축되었음을 증명하고 있다.

<img width="814" height="654" alt="スクリーンショット 2026-06-18 19 09 28" src="https://github.com/user-attachments/assets/b9062eaa-4a84-4eea-87b6-2a59dfc534a7" />
<img width="1157" height="411" alt="スクリーンショット 2026-06-19 20 40 34" src="https://github.com/user-attachments/assets/30c3e620-8585-4df6-b95a-1b583dcc9c40" />
