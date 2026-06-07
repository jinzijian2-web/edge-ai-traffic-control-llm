\# Adaptive Traffic Signal Control Using Edge AI and Local LLM Agent



\## Project Overview



This project implements an Edge AI-based adaptive traffic signal control prototype.



The system uses YOLOv8n to detect and count vehicles from a local traffic video, calculates adaptive green light duration based on the average vehicle count, and uses a local lightweight LLM agent to explain the traffic decision and recommend an action.



The project is designed to go beyond simple object detection. It continues the full pipeline from local vehicle detection to local traffic signal decision-making.



\## Key Idea



The system follows this pipeline:



```text

Local traffic video

&#x20;       ↓

Frame sampling

&#x20;       ↓

YOLOv8n vehicle detection

&#x20;       ↓

Vehicle counting

&#x20;       ↓

Average vehicle count calculation

&#x20;       ↓

Rule-based adaptive green light control

&#x20;       ↓

Local LLM agent explanation

&#x20;       ↓

Traffic decision and action output

```



The system is considered an Edge AI prototype because traffic video processing, YOLOv8n inference, adaptive signal control, and local LLM reasoning are all performed locally. Raw video frames are not uploaded to the cloud.



\## Main Features



\* Local vehicle detection using YOLOv8n

\* Vehicle counting for car, bus, truck, and motorcycle

\* Adaptive green light duration based on average vehicle count

\* Rule-based traffic state classification for stable decisions

\* Local lightweight LLM agent using Ollama and qwen2.5:0.5b

\* Privacy-preserving design: raw video stays local

\* CSV result output

\* YOLO detection sample image output

\* Local latency measurement for YOLO inference and LLM agent reasoning



\## Edge AI Design



This project is not only a computer vision or machine learning project. It is an Edge AI system because the complete process is executed locally.



\### Local Sensing



The traffic video is processed on the local machine. The system samples video frames locally and does not send the raw video to a cloud server.



\### Local Inference



YOLOv8n performs vehicle detection locally. Only vehicle-related classes are counted:



```text

car, bus, truck, motorcycle

```



\### Local Control



The rule-based controller calculates the traffic state and green light duration locally.



\### Local Reasoning



The local LLM agent runs through Ollama on localhost. It receives only structured metadata such as vehicle count, traffic state, green time, and latency.



\### Local Action



The system outputs adaptive green light duration, next detection cycle timing, congestion warning, monitoring action, and explanation.



\## Project Structure



Recommended repository structure:



```text

edge-ai-traffic-control-llm/

│

├── adaptive\_traffic\_control\_with\_llm.ipynb

├── traffic\_control\_with\_llm\_results\_fixed.csv

├── yolo\_detection\_sample.jpg

├── README.md

├── requirements.txt

└── .gitignore

```



The following files are not uploaded to GitHub due to file size or environment reasons:



```text

traffic.mp4

yolov8n.pt

.venv/

ollama\_models/

```



\## System Architecture



The system contains five main layers.



\### 1. Input Layer



The input is a local traffic video named:



```text

traffic.mp4

```



In this prototype, a recorded video is used to simulate a real traffic camera stream.



\### 2. Perception Layer



YOLOv8n detects vehicles from sampled frames. The controller filters only vehicle classes and ignores non-vehicle objects such as traffic lights, road signs, and background objects.



\### 3. Control Layer



The controller calculates the average vehicle count in each cycle and converts it into adaptive green light duration.



\### 4. Local LLM Agent Layer



The local LLM agent uses Ollama with qwen2.5:0.5b. It explains the decision and generates an operational action.



\### 5. Output Layer



The system outputs:



\* vehicle counts

\* average vehicle count

\* traffic state

\* green light time

\* yellow light time

\* next detection start time

\* LLM agent decision

\* LLM agent action

\* LLM explanation

\* privacy note

\* YOLO inference latency

\* LLM latency

\* CSV result file

\* YOLO detection sample image



\## Methodology



\### Frame Sampling



The system samples one frame every 10 seconds. Each control cycle uses three sampled frames.



For example, Cycle 1 samples:



```text

0s, 10s, 20s

```



If the green light time is 45 seconds and the yellow light time is 3 seconds, the next detection cycle starts after:



```text

45s + 3s = 48s

```



Therefore, Cycle 2 samples:



```text

48s, 58s, 68s

```



This creates an adaptive control loop instead of a fixed detection schedule.



\### YOLOv8n Vehicle Detection



YOLOv8n is used for local vehicle detection.



The counted vehicle classes are:



```python

VEHICLE\_CLASSES = {"car", "bus", "truck", "motorcycle"}

```



The detection function counts only these vehicle classes and ignores other detected objects.



\### Adaptive Signal Control Rule



The green light duration is calculated based on the average vehicle count.



Control parameters:



```python

MIN\_GREEN = 10

MAX\_GREEN = 45

YELLOW\_TIME = 3

EXTENSION\_THRESHOLD = 5

EXTENSION\_INCREMENT = 5

```



The traffic state is determined using local deterministic rules:



```text

average vehicle count >= 35  → Heavy traffic

average vehicle count >= 20  → Moderate traffic

average vehicle count >= 5   → Light traffic

average vehicle count < 5    → Very low traffic

```



The final green light duration is limited between 10 and 45 seconds.



\### Local Lightweight LLM Agent



The local LLM agent is deployed through Ollama using:



```text

qwen2.5:0.5b

```



The LLM receives only structured metadata:



```text

sample times

vehicle counts

average vehicle count

traffic state

green light time

YOLO inference latency

rule-based decision

rule-based action

```



Raw video frames are not sent to the LLM.



The traffic state and main control decision are determined by local rules for stability. The LLM is used for explanation, action generation, and privacy note.



\## Experimental Setup



| Item                     | Configuration                 |

| ------------------------ | ----------------------------- |

| Input video              | traffic.mp4                   |

| Video duration           | 414.85 seconds                |

| FPS                      | 29.97                         |

| Frame count              | 12,433                        |

| Detection model          | YOLOv8n                       |

| Local LLM                | Ollama + qwen2.5:0.5b         |

| LLM size                 | Approximately 494M parameters |

| Quantization             | Q4                            |

| Sampling interval        | 10 seconds                    |

| Frames per control cycle | 3                             |

| Maximum green light      | 45 seconds                    |

| Yellow light             | 3 seconds                     |

| Prototype device         | Local laptop                  |



\## Latest Experimental Results



The final system ran four adaptive control cycles.



| Cycle | Sample Times   | Vehicle Counts | Average Count | Traffic State | Green Time | Next Detection Start |

| ----: | -------------- | -------------- | ------------: | ------------- | ---------: | -------------------: |

|     1 | 0, 10, 20s     | 111, 100, 106  |        105.67 | Heavy traffic |        45s |                  48s |

|     2 | 48, 58, 68s    | 109, 104, 104  |        105.67 | Heavy traffic |        45s |                  96s |

|     3 | 96, 106, 116s  | 108, 107, 111  |        108.67 | Heavy traffic |        45s |                 144s |

|     4 | 144, 154, 164s | 117, 127, 120  |        121.33 | Heavy traffic |        45s |                 192s |



All four cycles were classified as heavy traffic because the average vehicle count was above the heavy-traffic threshold of 35.



Therefore, the controller assigned the maximum green light duration of 45 seconds in each cycle.



\## Local LLM Agent Output



The LLM agent explains the rule-based decision and recommends an action.



Example output:



```text

Traffic State: Heavy traffic

Decision: Extend green light to maximum or near-maximum duration.

Action: Generate congestion warning and continue local monitoring.

Privacy Note: Raw video is processed locally and is not sent to the cloud.

```



This shows that the system continues from vehicle detection to explainable decision-making.



\## Latency Results



\### YOLOv8n Inference Latency



| Cycle | Average YOLO Latency |

| ----: | -------------------: |

|     1 |            181.81 ms |

|     2 |            160.47 ms |

|     3 |            160.82 ms |

|     4 |            223.49 ms |



\### Local LLM Agent Latency



| Cycle | LLM Agent Latency |

| ----: | ----------------: |

|     1 |        1090.38 ms |

|     2 |         894.42 ms |

|     3 |         905.60 ms |

|     4 |         942.50 ms |



YOLOv8n handles fast local perception. The LLM agent is slower, but it is called only once per control cycle, not for every video frame. Therefore, the LLM latency is acceptable for this prototype.



\## Environment Setup



Create and activate a Python environment.



Example on Windows:



```bash

cd /d D:\\traffic\_project

python -m venv .venv

.venv\\Scripts\\activate

```



Install required packages:



```bash

pip install -r requirements.txt

```



\## Requirements



The `requirements.txt` file should include:



```text

ultralytics

opencv-python

numpy==1.26.4

pandas

requests

matplotlib

ipykernel

```



\## Local LLM Setup



Install Ollama from the official website.



Then pull the lightweight local LLM:



```bash

ollama pull qwen2.5:0.5b

```



Check whether the model is available:



```bash

ollama list

```



You should see:



```text

qwen2.5:0.5b

```



Test the local Ollama service:



```bash

curl http://127.0.0.1:11434/api/tags

```



\## How to Run



1\. Clone or download this repository.

2\. Install the required Python packages.

3\. Install Ollama.

4\. Pull the local LLM model:



```bash

ollama pull qwen2.5:0.5b

```



5\. Put a traffic video named `traffic.mp4` in the project folder.

6\. Open the notebook:



```text

adaptive\_traffic\_control\_with\_llm.ipynb

```



7\. Run all cells.



The system will output:



```text

traffic\_control\_with\_llm\_results\_fixed.csv

yolo\_detection\_sample.jpg

```



\## Important Notes



The following files are not included in the repository:



```text

traffic.mp4

yolov8n.pt

.venv/

```



Reasons:



\* `traffic.mp4` may be large.

\* `yolov8n.pt` can be downloaded automatically by Ultralytics.

\* `.venv/` is a local Python environment and should not be uploaded.



\## Why This Project Is Edge AI



This project is Edge AI because:



1\. Traffic video is processed locally.

2\. YOLOv8n detects and counts vehicles locally.

3\. Traffic state and green light duration are calculated locally.

4\. The LLM agent runs locally through Ollama.

5\. Raw video is not uploaded to the cloud.

6\. Only structured metadata is passed to the local LLM.



This design reduces privacy risk, network dependency, and cloud computation.



\## Limitations



This prototype has several limitations:



\* The input is a recorded video, not a live traffic camera stream.

\* The current experiment focuses on one traffic direction.

\* The selected video is consistently busy, so all cycles are heavy traffic.

\* The lightweight LLM may produce weak explanations if the prompt is not strict.

\* The system was tested on a local laptop, not yet deployed on Raspberry Pi or NVIDIA Jetson.



\## Future Work



Future improvements include:



\* Use live camera input.

\* Add multiple regions of interest for different traffic directions.

\* Compare busy road and normal road videos.

\* Detect emergency vehicles and add priority control.

\* Deploy the system on Raspberry Pi or NVIDIA Jetson.

\* Export YOLOv8n to ONNX for optimization.

\* Apply quantization or hardware acceleration.

\* Test stronger local LLMs for better explanations.



\## Authors



Jin Zijian 1236608

Hu Xinchen 1163910



\## License



This project is developed for academic coursework and demonstration purposes.



