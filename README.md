# Multi-Agent Reinforcement Learning for Communication-Free Exploration via Inter-Agent Trajectory Observation

## Original Version (w communication)
<div>
  <img src="docs/marvel_w_communication.gif">
<div>

## Our proposed method (w/o communication)
<div>
   <img src="docs/marvel_wo_communication.gif"/>
</div>

## 프로젝트 개요
- 주제: Multi-Agent Reinforcement Learning for constrained Field-of-View multi robot Exploration in Large-scale environments
- 목표: 여러 대의 로봇이 미지의 실내 환경을 가장 짧은 궤적으로 탐사해서 100%의 coverage를 100%로 달성하는 것
- Our method: 기본 방법에서 통신 없이도 다른 로봇이 어디서 왔는지 & 어디로 향하는 지를 보고, 중복 탐사를 피하고 암묵적으로 효울적인 action을 스스로 학습

## 환경 세팅 (Setup)
```bash
conda env create -f marvel.yml
conda activate marvel
```
Tested environment: ubuntu 22.04, cuda 12.4

## 학습 (Training)
```bash
python driver.py
```
- 학습 설정은 `parameter.py`에서 변경
- 체크포인트 저장 위치: `model/<FOLDER_NAME>/checkpoint.pth`
- TensorBoard 로그: `train/<FOLDER_NAME>/`

## 평가 (Evaluation)
```bash
python test_driver.py
```
- 평가 설정은 `test_parameter.py`에서 변경
- 결과: 콘솔 메트릭 + `record.txt`, GIF 저장 위치: `results/gifs/<LOAD_FOLDER_NAME>/`

## Parameter 설정

### parameter.py (학습용)
- `N_AGENTS`: 에이전트 수 (기본값: 4)
- `FOV`: 시야각 (기본값: 120°)
- `SENSOR_RANGE`: 센서 범위 (기본값: 10m)
- `EMBEDDING_DIM`: 임베딩 차원 (기본값: 128)
- `LR`: 학습률 (기본값: 1e-5)
- `BATCH_SIZE`: 배치 크기 (기본값: 256)
- `REPLAY_SIZE`: 리플레이 버퍼 크기 (기본값: 10000)
- `GAMMA`: 할인 계수 (기본값: 1)
- `MINIMUM_BUFFER_SIZE`: 학습 시작 최소 버퍼 크기 (기본값: 2000)
- `USE_GPU_GLOBAL`: 메인 프로세스 GPU 사용 여부 (기본값: True)
- `USE_GPU`: 워커 GPU 사용 여부 (기본값: False)

### test_parameter.py (평가용)
- `load_path`: 체크포인트 경로 (예: `model/test_2/checkpoint.pth`)
- `TEST_SET`: 테스트 맵 경로
- `NUM_META_AGENT`: 메타 에이전트 수
- `NUM_TEST`: 테스트 반복 횟수

## 보고서
[프로젝트 PPT](docs/강화학습개론_RL_project_7조_Explorer.pptx)