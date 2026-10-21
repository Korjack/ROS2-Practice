# 1단계 — ROS1 → ROS2 복구

## 목표

ROS1에서 알던 개념을 ROS2 방식으로 다시 손에 익힌다. 새로 배우는 게 아니라 **달라진 부분을 확인하는 단계**.

| ROS1 | ROS2 | 체감 포인트 |
|---|---|---|
| `roscore` (마스터) | 없음, DDS가 노드끼리 직접 탐색 | 마스터 없이 노드가 어떻게 서로를 찾는가 |
| `rospy` | `rclpy` | 노드가 클래스(`Node`) 기반, 콜백은 executor가 실행 |
| `catkin_make` | `colcon build` | 패키지마다 `setup.py`/`package.xml` |
| XML launch | Python launch (XML/YAML도 가능) | launch가 "코드"가 됨 |
| 전역 파라미터 서버 | 노드별 파라미터 | 파라미터가 노드에 속함 |
| — | QoS | 발행자/구독자 설정이 안 맞으면 통신이 조용히 안 됨 |
| actionlib | 내장 action | 사용법이 서비스와 비슷해짐 |

## 세션 과제

코드는 `ws/src/` 아래 `practice_basics` 패키지(`ament_python`)에 만든다.

- [ ] **S1. 환경 확인**
  `ros2 doctor --report`로 배포판 확인, `demo_nodes_py` talker/listener 실행.
  `roscore` 없이 통신되는 걸 보고, 다른 터미널에서 `ros2 node list` / `ros2 topic echo`.
  설치된 배포판 이름을 `logs/`에 기록.
- [ ] **S2. 워크스페이스와 패키지**
  `ros2 pkg create --build-type ament_python practice_basics`.
  `colcon build --symlink-install` 후 `source install/setup.bash`.
  `setup.py`의 `entry_points`가 무슨 역할인지 확인.
- [ ] **S3. Publisher / Subscriber**
  1초마다 증가하는 숫자를 `/counter`로 발행하는 노드, 받아서 짝수만 출력하는 노드.
  `create_timer`, `create_subscription`, `rclpy.spin`.
- [ ] **S4. Service**
  turtlesim을 띄우고 `/turtle1/set_pen` 서비스를 코드에서 호출해 펜 색을 바꾼다.
  이어서 직접 서비스 서버 하나 작성 (예: 문자열을 받아 뒤집어 반환).
- [ ] **S5. Parameter + Launch**
  S3의 발행 주기를 파라미터로 바꾸고, Python launch 파일 하나로 두 노드를 함께 실행.
  실행 중 `ros2 param set`으로 주기 변경이 반영되도록 콜백 추가.
- [ ] **S6. QoS 실험**
  발행자는 `BEST_EFFORT`, 구독자는 `RELIABLE`로 두고 통신이 되는지 확인.
  `ros2 topic info -v`로 원인 찾기. 반대 조합도 해보고 결과를 표로 정리.
- [ ] **S7. 미니 프로젝트 — turtlesim 제어**
  거북이가 원을 그리다가 벽 근처(`/turtle1/pose`)에 가면 회전해서 피하는 노드.
  `/turtle1/rotate_absolute` action으로 특정 각도까지 회전시키는 기능 포함.

## 완료 기준

- S3~S7 노드를 실행해 동작을 보여줄 수 있다.
- AI 없이 S3의 노드 하나를 처음부터 다시 작성할 수 있다 (문서 참고는 허용).

## 자가 점검 질문 (AI 없이 답하기)

1. `roscore`가 없는데 talker와 listener는 어떻게 서로를 찾는가?
2. `rclpy.spin()`을 호출하지 않으면 콜백은 어떻게 되는가? 왜 그런가?
3. `--symlink-install`을 빼면 Python 파일을 고칠 때마다 무엇을 해야 하는가?
4. S6에서 통신이 안 된 조합은 무엇이고, 왜 그 조합만 안 되는가?
5. 서비스와 action은 각각 어떤 상황에 쓰는가? turtlesim 회전에 action이 맞는 이유는?

## 막힐 때

- ROS2 공식 튜토리얼: <https://docs.ros.org/en/lyrical/Tutorials.html> (설치한 배포판 이름으로 URL의 `lyrical` 교체)
- QoS 개념: 공식 문서의 "About Quality of Service settings"
- ROS1 경험자용: 공식 문서의 "Migrating from ROS 1" 섹션
