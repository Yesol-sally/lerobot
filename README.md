# lerobot
lerobot를 활용한 시뮬레이션

참고 사이트: https://huggingface.co/docs/lerobot/main/installation

설치환경은 mac m1, miniconda 24.11.1 입니다.

lerobot에서 지원하는 시뮬레이터는 aloha, pusht 등이 있는데, 지원하는 로봇이 다르다고 보면 될 것 같습니다.

> aloha — ALOHA 시뮬레이터 / ALOHA 환경 지원
>
> pusht — PushT 환경 지원
>
> xarm — 로봇 팔(XArm 계열) 시뮬레이터 지원 
>
> feetech — 모터 제어 / Feetech 하드웨어 지원

## Installation

miniconda에서 python 3.10 버전의 가상환경을 만들어 줍니다. 3.11로는 확실히 오류가 떴습니다..

가상환경은 각각의 로봇 환경별로 따로 만들어 줍니다. (패키지 충돌 문제가 매우많기 때문에..)

저는 aloha를 설치해보았습니다.

### 환경세팅
```
conda create -y -n aloha python=3.10
conda activate lerobot
```

miniconda를 쓰면 다음과 같은 설치과정이 필요합니다.
```
conda install ffmpeg -c conda-forge
```

여기서 저는 python version이 계속 3.11로 떴는데, 알고보니 다른 conda 환경의 파이썬 버전이 적용되고 있었습니다.

`which python` 으로 확인했을 때, 방금 생성한 가상환경의 이름이 떠야 정상입니다.

그런데 저는 다른 가상환경 경로가 확인 되었습니다.

PATH 우선순위에 의한 문제였고 따라서, 임시로 다음과 같이 PATH 순서를 바꾸는 방법을 적용했습니다.

```
export PATH="$CONDA_PREFIX/bin:$PATH"   # conda env/bin을 PATH 맨앞에
hash -r
```

python 3.10으로 뜨는 것을 확인하고,


### lerobot install

```
git clone https://github.com/huggingface/lerobot.git
cd lerobot
```

라이브러리를 수정할수있는 모드로 변경합니다.
```
pip install -e .
```

**참고로 여기서 lerobot 브랜치 버전**을 바꿔주세요. 이것도 버전관련 문제인데

강의 해주시는 교수님께서 바꾸는게 정신건강에 좋을 것이라고 하셨습니다..

```
git checkout v0.3.3
```

아래의 코드에서 [all] 은 실행하면 안됩니다. 하나의 시뮬레이터에 대해서만 설치를 합니다..!!
```
pip install 'lerobot[all]'          # All available features
pip install 'lerobot[aloha,pusht]'  # Specific features (Aloha & Pusht)
pip install 'lerobot[feetech]'      # Feetech motor support
```

저는 aloha를 설치했습니다. .
```
pip install -e '.[aloha]’
```

리눅스 버전에서는 특히 버전 충돌 문제가 많다고 합니다. 아래와 같이 추가 디펜던시에 대한 코드가 있었는데.. 우선 저는 필요없었습니다.

> Troubleshooting
> If you encounter build errors, you may need to install additional dependencies: cmake, build-essential, and ffmpeg libs. To install these for linux run:
> 
> ```sudo apt-get install cmake build-essential python-dev pkg-config libavformat-dev libavcodec-dev libav```


## Quick Start

aloha에서 예제 파일을 실행해보았습니다.

아래 링크의 내용을 가져왔습니다.

https://github.com/huggingface/gym-aloha

```
pip install gym-aloha
```

```python
# example.py
import imageio
import gymnasium as gym
import numpy as np
import gym_aloha

env = gym.make("gym_aloha/AlohaInsertion-v0")
observation, info = env.reset()
frames = []

for _ in range(1000):
    action = env.action_space.sample()
    observation, reward, terminated, truncated, info = env.step(action)
    image = env.render()
    frames.append(image)

    if terminated or truncated:
        observation, info = env.reset()

env.close()
imageio.mimsave("example.mp4", np.stack(frames), fps=25)
```

### 실행결과


<img width="1252" height="892" alt="image" src="https://github.com/user-attachments/assets/001b5cdf-7bdf-472d-a544-0a7daade81fe" />
