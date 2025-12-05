# MARVEL 프로젝트 README (수업 제출용)

<div>
   <img src="results/gifs/MARVEL/50_6_120_10_explored_rate_0.9976.gif" height="240"/>
</div>

## 프로젝트 개요
- 주제: Multi-Agent Reinforcement Learning for constrained Field-of-View multi robot Exploration in Large-scale environments
- 목표: 여러 대의 로봇이 미지의 실내 환경을 가장 짧은 궤적으로 탐사해서 100%의 coverage를 100%로 달성하는 것
- Our method: 통신 없이도 다른 로봇이 어디서 왔는지 & 어디로 향하는 지를 보고, 중복 탐사를 피하고 암묵적으로 효울적인 action을 스스로 학습

## 실행 방법
### 1) 환경 구성
```bash
conda env create -f marvel.yml
conda activate marvel
```
Tested environment: ubuntu 24.04, cuda 12.8

### 2) 학습
```bash
python driver.py
```
- `parameter.py`에서 에이전트 수(`N_AGENTS`), 시야각(`FOV`), 센서 범위(`SENSOR_RANGE`), 학습 하이퍼파라미터(`LR`, `BATCH_SIZE`, `REPLAY_SIZE` 등) 조정
- 체크포인트: `model/<FOLDER_NAME>/checkpoint.pth`
- TensorBoard 로그: `train/<FOLDER_NAME>/`

### 3) 평가
```bash
python test_driver.py
```
- `test_parameter.py`에서 로드 경로(`load_path`), 테스트 맵(`TEST_SET`), 메타 에이전트 수(`NUM_META_AGENT`), 반복 횟수(`NUM_TEST`) 설정
- 결과: 콘솔 메트릭 + `record.txt`, GIF는 `results/gifs/<LOAD_FOLDER_NAME>/`


## 코드 / 데이터 구성
- 주요 스크립트: `driver.py`(학습), `test_driver.py`(평가), `parameter.py`/`test_parameter.py`(실험 설정)
- 환경/맵: `maps_medium`, `maps_test`의 2D occupancy grid
- 체크포인트/로그: `model/<run>`, `train/<run>`, 평가 GIF `results/gifs/<run>`

## 상태·행동·보상 설계
- **상태**: 로컬 그래프 노드(Utility/Occupancy), 이웃 간선, Frontier 분포(각도별), 방문 헤딩, 이웃 최적 헤딩, FoV 내 감지한 타 로봇 궤적(위치·헤딩·속도 시계열) 임베딩
- **행동**: Pointer 네트워크가 후보 노드/헤딩 쌍 중 다음 이동 지점 선택(36방향 이산 헤딩, `NUM_HEADING_CANDIDATES`로 후보 축소)
- **보상**:
  - 유틸리티 보상: 시야 내 Frontier 밀도 비례
  - 궤적 정렬 보상: 진행 방향과 이동 벡터 일치도
  - 팀 보상: 전체 탐사율 기반 가산 + 완료 시 보너스, 조기 종료/중복 탐사 시 감소

## 알고리즘
- 기반: SAC/MAAC 변형 (centralized critic 옵션), 그래프 Transformer 기반 Policy/Q 네트워크
- 궤적 인코더를 통해 통신 없이 주변 로봇 궤적을 활용
- 타깃 Q 네트워크 동기화, 리플레이 버퍼 오프폴리시 학습


## 주요 하이퍼파라미터 (예시)
- 에이전트 수: `N_AGENTS=4` (학습), 테스트 시 가변
- 시야각/센서 범위: `FOV=120°`, `SENSOR_RANGE=10m`
- 네트워크: `EMBEDDING_DIM=128`, Transformer 인코더/디코더 6/1층, Trajectory encoder 사용
- 학습: `BATCH_SIZE=256`, `LR=1e-5`, `REPLAY_SIZE=10000`, `GAMMA=1`, `MINIMUM_BUFFER_SIZE=2000`
- 자원: 메인 프로세스 GPU 사용(`USE_GPU_GLOBAL=True`), 워커는 기본 CPU(`USE_GPU=False`)

## 실험 결과 확인
- 주요 지표: `travel_dist`, `explored_rate`, `success_rate`, `overlap_ratio`, 특정 탐사율(예: 0.9) 도달 거리
- GIF로 각 에피소드 탐사 과정을 시각화(`results/gifs/...`)
- 비교 실험 결과

## 학습된 모델 (checkpoint)
- model/test_2/checkpoint.pth  
- 다른 모델 사용 시에는 parameter.py에서 변경

## PPT 보고서
[프로젝트 PPT](docs/강화학습개론_RL_project_7조_Explorer.pptx)


