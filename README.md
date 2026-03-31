# AMF — Autonomous Mobile Forklift

ROS2 Humble 기반 자율주행 지게차 시스템.  
TurtleBot3 Waffle Pi + 자체 제작 포크리프트 모듈로 **상차 → 주행 → 하차** 풀 파이프라인을 구현합니다.

---

## 디렉토리 구조

```
AMF/
├── remote_pc/          # 리모트 PC — 주행 두뇌 + 웹 서버 + LLM
│   └── src/fp_pkg/
│       ├── fp_pkg/         # ROS2 노드 (db_astar, hey_yang, yolo_slow 등)
│       ├── blueprints/     # Flask Blueprint (drive, map, node, alert)
│       ├── database/       # DB 서비스 (map, node, alert)
│       ├── utils/          # LLM 핸들러, 맵 매니저
│       ├── templates/      # 웹 UI HTML
│       ├── static/         # CSS
│       ├── launch/         # ROS2 런치 파일
│       ├── models/         # YOLO 모델 (.pt)
│       └── app.py          # Flask 서버 진입점
│
└── raspberry_pi/       # 라즈베리파이 — 상차 노드 + 아두이노 브릿지
    └── src/fp_pkg/
        └── fp_pkg/
            ├── carry_node.py   # 상차 노드 (ArUco + Fork UP)
            └── sensor_node.py  # 아두이노 브릿지 (LED·부저·비상정지)
```

---

## 실행 방법

### 리모트 PC

```bash
cd remote_pc/src/fp_pkg
python app.py   # Flask 서버 + ROS2 노드 통합 실행 (port 5000)
```

하차 노드 별도 실행:
```bash
ros2 run fp_pkg hey_yang
```

### 라즈베리파이

```bash
cd raspberry_pi
colcon build --packages-select fp_pkg
source install/setup.bash

ros2 run fp_pkg carry_node    # 상차 노드
ros2 run fp_pkg sensor_node   # 아두이노 브릿지
```

---

## 기술 스택

| 분야 | 기술 |
|---|---|
| 로봇 미들웨어 | ROS2 Humble |
| 위치 추정 | AMCL |
| 경로 계획 | A\* (커스텀) |
| 경로 추종 | Pure Pursuit |
| 장애물 감지 | YOLOv8 |
| 마커 인식 | OpenCV ArUco |
| 웹 서버 | Flask + Socket.IO |
| DB | MySQL / SQLite |
| LLM | Ollama (qwen2.5:3b) |
| 하드웨어 | Arduino + SG90 서보 |

---

## 요구 사항

```bash
pip install ultralytics pyserial opencv-python numpy \
            flask flask-socketio flask-cors rclpy
sudo apt install ros-humble-cv-bridge ros-humble-nav2-bringup
```
