# 3단계 — 강화학습 기초

## 목표

"보상만 주고 행동은 스스로 찾게 하는" 학습을 직접 돌려보고, **보상을 어떻게 설계하느냐가 결과를 바꾼다**는 걸 체감한다.
ROS2 없이 Python만으로 진행 (Mac에서도 가능).

## 핵심 개념

- **환경 루프**: 관측(observation) → 행동(action) → 보상(reward) → 다음 관측. 끝나면 `terminated` / `truncated`
- **MuJoCo**: 빠르고 CPU로 잘 도는 물리 엔진. 로봇 학습에서 많이 씀
- **Gymnasium**: 환경 인터페이스 표준
- **PPO**: 가장 무난한 강화학습 알고리즘. Stable-Baselines3(SB3)로 사용

## 세션 과제

코드는 `py/rl/` 아래.

- [ ] **S1. MuJoCo 맛보기**
  `pip install mujoco` 후 예제 모델을 viewer로 띄우기.
  Python에서 `mj_step`을 직접 돌리며 관절에 토크를 넣고 상태(`qpos`, `qvel`)가 변하는 걸 출력.
- [ ] **S2. Gymnasium 루프**
  CartPole을 **무작위 행동**으로 돌리는 루프를 직접 작성. 에피소드 길이·누적 보상 출력.
  관측·행동 공간(`observation_space`, `action_space`)이 무엇인지 기록.
- [ ] **S3. PPO 학습**
  SB3 PPO로 CartPole 학습 → 이어서 MuJoCo `Reacher` 환경 학습.
  학습 전·후 영상 또는 보상 비교. CPU 학습 시간 기록.
- [ ] **S4. 보상 실험**
  Reacher 보상을 래퍼로 바꿔서 3가지 버전 학습: ① 기본 ② 거리 보상만 ③ 도착 시에만 +1 (희소 보상).
  결과가 왜 다른지 가설 → 확인 → 기록.
- [ ] **S5. 내 팔로 환경 만들기**
  2단계의 2관절 팔을 MJCF(MuJoCo 모델 포맷)로 옮기고, 무작위 목표점에 손끝을 가져가는 커스텀 Gymnasium 환경 작성.
  `gymnasium.utils.env_checker`로 검증.
- [ ] **S6. 학습과 평가**
  S5 환경을 PPO로 학습. TensorBoard로 보상 곡선을 보고, 평가 스크립트로 성공률(손끝-목표 거리 < 기준) 측정.
  모델 파일은 `py/rl/models/`에 저장 (git에는 올리지 않음).

## 완료 기준

- 직접 만든 팔 환경에서 학습된 정책이 목표점에 도달한다 (성공률 수치 기록).
- S4 결과를 근거로 보상 설계가 결과를 바꾼 이유를 설명할 수 있다.

## 자가 점검 질문

1. 관측에 "목표점 좌표"를 빼면 학습이 어떻게 될까? 왜?
2. 희소 보상(S4-③)이 잘 안 되는 이유는? 어떻게 보완할 수 있나?
3. `terminated`와 `truncated`의 차이는? 섞어 쓰면 어떤 문제가 생기나?
4. 보상 곡선은 올라가는데 실제 동작이 이상하다면 무엇을 의심해야 하나? (보상 해킹)
5. PPO가 "on-policy"라는 건 무슨 뜻이고, 그래서 어떤 비용이 드는가?

## 막힐 때

- MuJoCo: <https://mujoco.readthedocs.io>
- Gymnasium: <https://gymnasium.farama.org>
- Stable-Baselines3: <https://stable-baselines3.readthedocs.io>
