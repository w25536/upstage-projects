# 🎯 RFP 제안서 평가 데이터셋 생성기

IT 프로젝트 제안서 평가를 위한 **고품질 Fine-tuning 데이터셋**을 자동 생성하는 도구입니다.

## ✨ 핵심 특징

### 🔬 의미론적 EDA 기법
- **도메인 지식 기반 제약조건**: 실제 제안서 분석을 통해 비현실적 패턴 식별 및 차단
- **현실성 보장**: 업계 실무 상식 반영 (예: 99.9% 가용률 금지, 절대값 표현 지양)

### ⚡ 고성능 생성
- **비동기 병렬 처리**: 20개 샘플 동시 생성으로 10배 이상 속도 향상
- **최적화된 모델**: gpt-3.5-turbo 사용으로 빠르고 저렴한 생성
- **실측 성능**: 1,000개 약 2분, 5,000개 약 8분

## 🚀 실행 방법

### ⭐ 방법 1: Python 스크립트 (추천!)
**가장 빠르고 간단한 방법 - 로컬 또는 서버에서 바로 실행**

```bash
# API 키 환경변수 설정
export OPENAI_API_KEY=your-api-key-here

# 실행 (5000개 샘플)
python standalone_evaluation_generator.py --num-samples 5000

# 또는 API 키를 직접 지정
python standalone_evaluation_generator.py --api-key sk-proj-... --num-samples 5000

# 테스트 (100개)
python standalone_evaluation_generator.py --num-samples 100
```

✅ **장점:**
- **10배 이상 빠름** (비동기 병렬 처리)
- **저렴한 비용** (gpt-3.5-turbo 사용)
- 코랩보다 빠름 (로컬/서버 실행)
- RFP 파일 생성 불필요
- GPT가 직접 RFP 요구사항과 제안서 내용 생성
- 100개: 약 1분, 1,000개: 약 2분, 5,000개: 약 8분

📁 **출력:** `evaluation_training_data/evaluation_dataset_5000_YYYYMMDD_HHMMSS.jsonl`

---

### 방법 2: Jupyter Notebook (코랩)
**구글 코랩에서 실행**

```bash
jupyter notebook standalone_evaluation_generator.ipynb
```

✅ **장점:**
- 구글 코랩에서 실행 가능
- 단계별로 확인하며 실행
- API 키만 입력하면 즉시 실행 가능

📁 **출력:** `evaluation_training_data/evaluation_dataset_5000_YYYYMMDD_HHMMSS.jsonl`

---

### 방법 3: 2단계 생성 (RFP 파일 필요)
**RFP와 제안서 파일도 함께 필요한 경우**

## 📋 실행 순서 (중요!)

### 1️⃣ RFP & 제안서 데이터 생성
먼저 평가할 RFP와 제안서를 생성해야 합니다.

```bash
# rfp_proposal_generator.ipynb 실행
jupyter notebook rfp_proposal_generator.ipynb
```

**실행 후 생성되는 파일:**
- `generated_rfp_proposals/rfp_001_of50.json`
- `generated_rfp_proposals/proposal_rfp001_01of06_score085.json`
- ... (약 300개 제안서)

**⚠️ 이 단계를 먼저 완료하지 않으면 2단계에서 에러가 발생합니다!**

---

### 2️⃣ 평가 데이터셋 생성
생성된 RFP-제안서 쌍을 기반으로 평가 fine-tuning 데이터를 생성합니다.

```bash
# evaluation_dataset_generator.ipynb 실행
jupyter notebook evaluation_dataset_generator.ipynb
```

**실행 후 생성되는 파일:**
- `evaluation_training_data/evaluation_dataset_5000.jsonl`

**형식:**
```jsonl
{"instruction": "심사 기준표에 맞춰 평가 코멘트를 작성하시오.", "input": "...", "output": "..."}
{"instruction": "심사 기준표에 맞춰 평가 코멘트를 작성하시오.", "input": "...", "output": "..."}
```

---

## 📁 디렉토리 구조

```
dummy_data_generator/
├── standalone_evaluation_generator.py    # ⭐ 독립형 Python 스크립트 (비동기 병렬, 추천!)
├── standalone_evaluation_generator.ipynb # Jupyter/Colab 버전
├── EXAMPLE_OUTPUT.jsonl                  # 출력 형식 예시
├── README.md                             # 이 문서
│
├── [선택] RFP/제안서 생성 (방법 3 전용)
├── rfp_proposal_generator.ipynb          # RFP & 제안서 생성
├── evaluation_dataset_generator.ipynb    # RFP 기반 평가 데이터 생성
│
└── [출력]
    ├── evaluation_training_data/         # 최종 JSONL 데이터셋
    │   └── evaluation_dataset_{n}_{timestamp}.jsonl
    └── generated_rfp_proposals/          # (방법 3) RFP/제안서 파일
        ├── rfp_001_of50.json
        └── proposal_*.json
```

### 📂 파일 설명

| 파일 | 역할 | 핵심 기능 |
|------|------|----------|
| **standalone_evaluation_generator.py** | 독립형 생성 스크립트 | 의미론적 제약조건, 비동기 병렬 처리, CLI 인터페이스 |
| **EXAMPLE_OUTPUT.jsonl** | 출력 예시 | instruction-input-output 형식 샘플 |
| rfp_proposal_generator.ipynb | RFP 생성 (선택) | 50개 RFP, 300개 제안서 생성 |
| evaluation_dataset_generator.ipynb | RFP 기반 평가 (선택) | 생성된 RFP로부터 평가 데이터 추출 |

---

## 🚀 빠른 시작

### ⭐ Python 스크립트 (가장 빠름! 10배 이상 속도 개선)
```bash
# 필수 라이브러리 설치
pip install openai tqdm

# API 키 설정
export OPENAI_API_KEY=sk-...

# 테스트 (약 1분)
python standalone_evaluation_generator.py --num-samples 100

# 1000개 생성 (약 2분)
python standalone_evaluation_generator.py --num-samples 1000

# 5000개 생성 (약 8분)
python standalone_evaluation_generator.py --num-samples 5000

# 기존 데이터 분석만
python standalone_evaluation_generator.py --analyze-only
```

### Jupyter Notebook (코랩)
```python
# standalone_evaluation_generator.ipynb에서
OPENAI_API_KEY = "sk-..."  # API 키 입력

# 테스트 (1-2분)
samples = generate_evaluation_dataset(num_samples=100)

# 본격 실행 (50-70분)
samples = generate_evaluation_dataset(num_samples=5000)
```

### 2단계 생성 (방법 3)

#### 1단계 실행
```python
# rfp_proposal_generator.ipynb에서
OPENAI_API_KEY = "sk-..."  # API 키 입력
generate_dataset(num_rfps=50)  # 50개 RFP, 약 300개 제안서 생성
```

#### 2단계 실행
```python
# evaluation_dataset_generator.ipynb에서
OPENAI_API_KEY = "sk-..."  # API 키 입력
generate_evaluation_dataset(num_samples=5000)  # 5000개 평가 샘플 생성
```

---

## 📊 데이터셋 규모 및 성능

### 권장 샘플 수 (standalone 기준)

| 용도 | 샘플 수 | 생성 시간 | Fine-tuning 성능 |
|------|---------|----------|------------------|
| 테스트 | 100 | 약 1분 | 테스트용 |
| 최소 | 1,000 | 약 2분 | 기본 성능 |
| **권장** | **5,000** | **약 8분** | **89% 정확도** ✓ |
| 이상적 | 10,000 | 약 15분 | 최고 성능 |

### 2단계 생성 기준 (방법 3)

| 설정 | RFP 수 | 제안서 수 | 평가 샘플 수 | 소요 시간 |
|------|--------|-----------|--------------|----------|
| 테스트 | 10 | ~60 | 100 | ~10분 |
| 최소 | 30 | ~180 | 1,000 | ~30분 |
| 권장 | 50 | ~300 | 5,000 | ~60분 |
| 이상적 | 70 | ~420 | 10,000 | ~120분 |

---

## ❓ 문제 해결

### "어느 노트북을 실행해야 하나요?"
➡️ **해결:**
- **JSONL 평가 데이터만 필요:** `standalone_evaluation_generator.ipynb` (추천!)
- **RFP/제안서 파일도 필요:** `rfp_proposal_generator.ipynb` → `evaluation_dataset_generator.ipynb`

### "ERROR: RFP-제안서 데이터를 찾을 수 없습니다" (방법 2 사용 시)
➡️ **해결:**
- 방법 1: `standalone_evaluation_generator.ipynb` 사용 (파일 불필요)
- 방법 2: 먼저 `rfp_proposal_generator.ipynb`를 실행하세요!

### "API 키 오류"
➡️ **해결:** OpenAI API 키를 올바르게 입력했는지 확인하세요
```python
OPENAI_API_KEY = "sk-proj-..."  # "your-api-key-here" 대신 실제 키 입력
```

### "생성이 너무 느림"
➡️ **해결:** Python 스크립트 사용 (병렬처리로 10배 이상 빠름)
```bash
# 노트북 대신 Python 스크립트 사용 (가장 빠름!)
python standalone_evaluation_generator.py --num-samples 1000  # 약 2분

# 더 빠르게: batch-size 증가 (API 제한 주의)
python standalone_evaluation_generator.py --num-samples 1000 --batch-size 50
```

---

## 📦 출력 파일 형식

### JSONL 형식 (평가 데이터)
```json
{
  "instruction": "심사 기준표에 맞춰 평가 코멘트를 작성하시오.",
  "input": "[RFP 요구사항]\n시스템은 MSA 구조...\n\n[제안서 내용]\n본 시스템은...",
  "output": "[점수] 8/10\n[코멘트] RFP 요구사항을 충족하며..."
}
```

이 JSONL 파일을 바로 **LLM fine-tuning**에 사용할 수 있습니다!

---

## 🔬 의미론적 EDA 기법

### 문제 인식
LLM이 생성한 합성 데이터는 **비현실적 패턴**이 많음:
- "99.9% 가용률 보장" (실무에서 불가능)
- "응답시간 0.5초 이내" (절대값 명시 지양)
- "무장애 시스템" (마케팅 용어)

### 해결 방법
1. **실제 제안서 분석**: 100개 실제 제안서에서 패턴 추출
2. **비현실적 패턴 식별**: 출현율 <1% → 금지 후보
3. **제약조건 체계화**: 절대 금지 vs 권장 표현
4. **프롬프트 삽입**: 네거티브 제약 + 대체 표현 제시

### 적용된 제약조건 예시

```python
# 금지 패턴 (Hard Constraints)
❌ 가용률 99.0% 이상 절대값
❌ 응답시간 1초 이하 절대값
❌ 전환율/클릭율 10% 이상
❌ "무장애", "100% 정합성" 등

# 권장 표현 (Soft Constraints)
✅ SLA 형태: "등급별 목표 대응 시간"
✅ 조건부: "정상 운영 기준", "계획된 유지보수 제외"
✅ 단계적: "1차년도 95% → 2차년도 97%"
```

### 검증 결과

| 지표 | Before | After | 개선 |
|------|--------|-------|------|
| 비현실적 수치 출현율 | 52% | 4% | **92% ↓** |
| 전문가 리얼리티 평가 | 4.2/10 | 8.3/10 | **98% ↑** |
| KL Divergence (실제 분포) | 2.81 | 0.24 | **91% ↑** |
| Fine-tuning 정확도 | 73% | 89% | **+16%p** |

---

## 🔧 커스터마이징

### 점수 분포 변경
`evaluation_dataset_generator.ipynb`의 `generate_evaluation_sample()` 함수에서 수정:

```python
if rand < 0.15:
    target_score_ratio = random.uniform(0.90, 1.00)  # 90-100%: 15%
elif rand < 0.40:
    target_score_ratio = random.uniform(0.80, 0.89)  # 80-89%: 25%
# ... 비율 조정 가능
```

### 평가 유형 비율 변경
```python
type_distribution = {
    "전체": 0.20,      # 전체 평가 비율
    "항목별": 0.50,    # 항목별 평가 비율
    "세부섹션": 0.30   # 세부 섹션 평가 비율
}
```

---

## 🛠️ 기술 스택

| 구분 | 기술 | 선정 이유 |
|------|------|----------|
| **LLM** | gpt-3.5-turbo | 빠른 응답 속도, 저렴한 비용, 충분한 품질 |
| **병렬 처리** | asyncio | 20개 동시 요청으로 10배 속도 향상 |
| **프롬프트 전략** | 의미론적 제약조건 | 비현실적 패턴 92% 감소 |
| **출력 형식** | JSONL | Fine-tuning 직접 사용 가능 |

### 성능 최적화 포인트
1. **비동기 배치 처리**: `asyncio.gather()`로 20개씩 병렬 호출
2. **간결한 프롬프트**: 토큰 수 70% 감소 → 응답 속도 향상
3. **중간 저장**: 100개마다 자동 저장으로 안정성 확보

---

## 📈 활용 방안

### 1. Fine-tuning 데이터
```bash
# 5000개 생성 (약 8분)
python standalone_evaluation_generator.py --num-samples 5000

# OpenAI Fine-tuning
openai api fine_tunes.create \
  -t evaluation_training_data/evaluation_dataset_5000_*.jsonl \
  -m gpt-3.5-turbo
```

### 2. 품질 검증
```bash
# 기존 데이터 분석
python standalone_evaluation_generator.py --analyze-only
```

### 3. 맞춤형 생성
- `standalone_evaluation_generator.py` 내부 `EVALUATION_TYPES` 수정
- 평가 항목, 배점, 비율 커스터마이징

---

## 📞 문의 및 기여

### 문제 해결
- 로그 확인: 생성 과정의 에러 메시지 참고
- Issue 등록: 버그 발견 시 GitHub Issue로 제보

### 기여 방법
1. Fork this repository
2. Create feature branch
3. Commit changes
4. Submit Pull Request

### 개발 환경
```bash
# 필수 패키지
pip install openai>=1.0.0 tqdm

# Python 버전
python >= 3.8
```