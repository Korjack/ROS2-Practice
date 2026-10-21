# 2단계 — 시뮬 속 로봇

## 목표

직접 정의한 로봇팔을 시뮬레이터에 띄우고, ROS2로 관절을 움직인다.
여기서 만든 2관절 팔이 3~5단계 내내 쓰이는 "내 로봇"이 된다.

## 핵심 개념

- **URDF / xacro**: 로봇의 링크(몸체)와 조인트(관절) 정의
- **TF**: 좌표계 간 변환. "손끝이 지금 어디 있는가"를 계산
- **Gazebo (gz sim)**: 물리 시뮬레이터. ROS2 배포판마다 짝이 되는 Gazebo 버전이 정해져 있음 → 설치 문서에서 확인
- **ros2_control**: 관절 명령을 하드웨어(또는 시뮬)에 전달하는 표준 계층

## 세션 과제

패키지: `ws/src/my_arm_description`(URDF), `ws/src/my_arm_control`(노드)

- [ ] **S1. URDF 작성**
  바닥 고정 베이스 + 2개 회전 관절 + 2개 링크의 평면 팔을 xacro로 작성.
  `robot_state_publisher` + `joint_state_publisher_gui` + `rviz2`로 슬라이더를 움직여 확인.
- [ ] **S2. TF와 정기구학**
  링크 길이 L1, L2와 관절각 θ1, θ2로 손끝 (x, y)를 **손으로 계산하는 식**을 직접 유도.
  `ros2 run tf2_ros tf2_echo base_link tool0` 값과 비교해서 일치 확인.
- [ ] **S3. Gazebo에 띄우기**
  URDF에 관성·충돌 정보 추가 → gz sim에 spawn.
  `ros_gz_bridge`로 시뮬 시간(`/clock`)과 관절 상태를 ROS2로 연결.
- [ ] **S4. ros2_control로 관절 제어**
  `joint_state_broadcaster` + 위치 컨트롤러(`forward_position_controller` 또는 `joint_trajectory_controller`) 설정.
  토픽 명령 하나로 팔이 지정 각도로 이동하는지 확인.
- [ ] **S5. 미니 프로젝트 — 목표 지점으로 이동**
  `/target_point`(x, y)를 받으면 **역기구학을 직접 계산**해 관절 명령을 보내는 노드.
  도달 불가능한 점(팔 길이 밖)이 들어오면 어떻게 처리할지 정해서 구현.

## Steam Deck 주의

- Gazebo 화면이 안 뜨거나 매우 느리면: 컨테이너라면 GPU/디스플레이 연결 설정 확인 → 그래도 안 되면 headless(`-s`)로 돌리고 rviz2로만 확인 → 최후에 Ubuntu PC로 Gazebo만 분리.
- 막힌 내용은 `logs/`에 꼭 기록 (같은 문제가 5단계에서 다시 나옴).

## 완료 기준

- 직접 만든 팔이 Gazebo 안에서 `/target_point` 명령대로 움직인다.
- 정기구학·역기구학 식을 AI 없이 다시 유도할 수 있다.

## 자가 점검 질문

1. URDF의 `visual`, `collision`, `inertial`은 각각 누가 쓰는가? 하나를 빼면 무엇이 깨지는가?
2. rviz2에서는 잘 보였는데 Gazebo에서 팔이 축 늘어진다면 원인 후보는?
3. `use_sim_time`을 켜지 않으면 어떤 문제가 생기는가?
4. 2관절 팔의 역기구학 해가 보통 2개인 이유는? 어느 쪽을 골랐는가?
5. ros2_control이 없다면 S4를 어떻게 했어야 하는가? 이 계층이 있어서 얻는 건?

## 막힐 때

- URDF 튜토리얼: ROS2 공식 문서 "URDF" 섹션
- Gazebo: <https://gazebosim.org/docs>
- ros2_control: <https://control.ros.org>
