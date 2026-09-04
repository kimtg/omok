# 19×19 AI 오목 (Omok)

[![License: Unlicense](https://img.shields.io/badge/license-Unlicense-blue.svg)](http://unlicense.org/)
[![HTML5](https://img.shields.io/badge/HTML5-Canvas-E34F26?logo=html5&logoColor=white)](https://developer.mozilla.org/ko/docs/Web/HTML)
[![JavaScript](https://img.shields.io/badge/JavaScript-ES6+-F7DF1E?logo=javascript&logoColor=black)](https://developer.mozilla.org/ko/docs/Web/JavaScript)
[![Zero Dependencies](https://img.shields.io/badge/Dependencies-Zero-success)](#)

HTML5 Canvas와 순수 JavaScript로 구현된 **19×19 표준 바둑판 규격의 인공지능(AI) 대전 오목 게임**입니다.  
별도의 설치 과정이나 빌드 도구 없이, 웹 브라우저에서 `index.html`을 여는 것만으로 즉시 플레이할 수 있습니다.

---

## ✨ 주요 기능

- **정통 19×19 규격 바둑판 렌더링**
  - 표준 19줄 격자 및 9개의 화점(Star points) 정밀 배치
  - 고급스러운 원목 나뭇결 배경 텍스처 및 입체 테두리
  - 실제 바둑돌 느낌의 3D 볼록 그라데이션 및 부드러운 그림자 효과
  - Retina 및 고해상도 디스플레이(DPI 스케일링) 지원
  - 착수 가이드(반투명 고스트 돌) 및 마지막 착수 위치 표시(레드 포인트)
  - 5목 완성 시 승리 골드 링 및 관통선 하이라이트

- **패턴 휴리스틱 기반 고성능 오목 AI**
  - 가로, 세로, 양방향 대각선의 오목 패턴 정밀 분석 (5목, 열린 4, 닫힌 4, 열린 3, 닫힌 3, 쌍삼, 사삼 등)
  - 플레이어의 공격을 즉각 감지하여 빈틈없이 방어하고, 기회가 오면 승리를 결정짓는 지능적인 착수
  - 3단계 난이도 선택: **쉬움 (Easy)** / **보통 (Normal)** / **어려움 (Hard)**
  - 361칸 중 유효 착수 주변부 후보수를 압축 탐색하여 딜레이 없이 즉각 반응

- **편의 및 대전 기능**
  - **진영 선택**: 플레이어 흑돌(선공) 또는 백돌(후공, AI가 흑돌 선공) 자유 선택
  - **무르기 (Undo)**: 실수했을 때 직전 턴 되돌리기 지원
  - **Web Audio API 내장 사운드**: 외부 오디오 파일 없이 웹 브라우저 자체 신디사이저로 바둑돌의 경쾌한 '딱' 소리와 승리 효과음 구현 (음소거 토글 가능)
  - **대전 전적 기록**: `localStorage`를 통해 승/패/무 통계 자동 저장 및 초기화 기능
  - **완전 무설치 (Zero Dependencies)**: 로컬 파일(`file://`)로 직접 열어도 CORS 제약 없이 동작

---

## 🚀 빠른 시작 (Getting Started)

### 1. 직접 열기 (가장 간단)
저장소를 클론하거나 다운로드한 후, `index.html` 파일을 더블 클릭하여 브라우저(Chrome, Edge, Safari 등)로 바로 실행합니다.

```bash
git clone https://github.com/YOUR_USERNAME/omok.git
cd omok
# index.html을 브라우저로 열기
```

### 2. 로컬 웹 서버로 실행 (선택 사항)
Python을 사용하여 간단한 로컬 HTTP 서버를 띄울 수도 있습니다.

```bash
# Python 3
python -m http.server 8000
```
브라우저에서 `http://localhost:8000` 주소로 접속합니다.

---

## 📁 프로젝트 구조

```
omok/
├── index.html          # 게임 메인 UI 구조 및 대시보드
├── style.css           # 반응형 레이아웃 및 다크 모던 테마 스타일
├── js/
│   ├── board.js        # 19x19 보드 모델, 착수 검증, 5목 판정, 무르기 로직
│   ├── ai.js           # 패턴 평가 휴리스틱 및 난이도별 최적 수 계산 엔진
│   └── game.js         # Canvas 그래픽 렌더링, Web Audio 효과음 및 게임 제어
├── test_runner.html    # 규칙 및 AI 수비/공격 자동 단위 테스트 러너
├── LICENSE             # The Unlicense 라이선스 전문
└── README.md           # 프로젝트 문서
```

---

## 🧠 AI 알고리즘 소개

본 프로젝트의 AI는 19×19 크기의 방대한 탐색 공간(361칸)에서 빠르고 사람다운 수를 두기 위해 **패턴 기반 휴리스틱 평가 함수(Heuristic Pattern Evaluator)**를 채택하고 있습니다.

1. **후보 수 압축 (Candidate Pruning)**:  
   전체 361칸을 매번 전수조사하는 대신, 이미 돌이 놓여진 위치의 주변 2칸 이내 유효한 빈 자리만 후보로 압축하여 연산 속도를 극대화합니다.
2. **패턴 가중치 분석 (Pattern Weighting)**:  
   각 후보 위치에 대해 4방향(가로, 세로, 대각선 2방향)으로 연속된 돌의 개수와 양 끝 개방 여부(`openEnds`)를 분석하여 점수를 산출합니다.
   - `5목 (승리)`: 100,000점 (최우선)
   - `열린 4 (Open 4)`: 15,000점 (다음 턴 무조건 승리)
   - `닫힌 4 (Blocked 4)`: 2,500점
   - `열린 3 (Open 3)`: 2,000점
   - `복합 위협 (쌍삼, 사삼, 쌍사)`: 추가 가산점
3. **공격 및 수비 점수 결합 (Attack & Defense Balance)**:  
   - 상대방의 5목 또는 열린 4 형성을 최우선 차단합니다.
   - 자신이 즉시 이길 수 있는 자리가 있다면 수비보다 승리 착수를 우선합니다.
   - 난이도 설정에 따라 수비 가중치와 착수 오차(노이즈)를 조절합니다.

---

## 🧪 테스트 실행

브라우저에서 `test_runner.html`을 열면 아래 핵심 기능들에 대한 자동 단위 테스트(Unit Tests) 결과를 확인할 수 있습니다:
- 보드 크기(19×19) 및 초기 상태 검증
- 착수 유효성, 중복 착수 방지, 경계 조건 체크
- 가로, 세로, 대각선 5목 승리 판정
- AI의 상대방 4목 즉시 방어 로직 검증
- AI의 5목 완성 즉시 승리 착수 로직 검증
- 무르기(Undo) 시 보드 및 히스토리 정합성 복구 검증

---

## 📄 라이선스 (License)

이 프로젝트는 [The Unlicense](LICENSE)에 따라 퍼블릭 도메인으로 배포됩니다.  
누구나 상업적/비상업적 목적을 불문하고 자유롭게 복제, 수정, 배포, 사용할 수 있습니다.

