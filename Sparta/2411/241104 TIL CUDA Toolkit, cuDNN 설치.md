#TIL #스파르타코딩클럽 [[2411]]

## 1. 전제 조건
1. python 설치
2. Anaconda3 설치


## 2. Nvidia 드라이버 설치
-  Nvidia 드라이버가 설치되어 있는지 확인
	1) 시작 - cmd 입력
	2) 명령 프롬프트 창에 nvisia-smi 입력
	![[nvidia-smi.png]]
		- 우측 상단의 CUDA Version은 설치 가능한 버전의 최대 범위이지 설치된 것은 아님

	3) 만약 드라이버가 없는 경우 아래 링크에서 자신의 GPU와 OS에 맞게 직접 설치
		- [Download Nvidia driver](https://www.nvidia.com/en-us/drivers/)


## 3. GPU와 호환되는 CUDA Toolkit 버전 체크
1. 아래의 링크에서 자신의 GPU 검색
	- [CUDA](https://en.wikipedia.org/wiki/CUDA#GPUs_supported)
2. 자신의 Compute capability 버전의 값을 기억하고, 표의 초록색 부분을 확인
	- Compute capability 버전 = 8.6 인 경우, CUDA SDK 버전은 11.1 이상 설치
![[gpu_wiki.png]]
3. 안정적인 PyTorch 버전 설치를 위해 아래의 링크에서 호환되는 CUDA 버전 확인
	- [Start Locally](https://pytorch.org/get-started/locally/)


## 4. CUDA Toolkit 설치
1. 아래의 링크에서 설치하고자 하는 CUDA Toolkit 버전을 다운로드
	- 자신의 OS, 컴퓨팅 환경에 맞게 조건 선택 후 다운로드 
	- [CUDA Toolkit archive](https://developer.nvidia.com/cuda-toolkit-archive)
	![[cuda_toolkit.png]]
2. 다운로드 후 실행 파일을 누르고 설치
	- 설치가 완료되면 컴퓨터 재부팅
3. CUDA 설치 여부 확인
	- 명령 프롬프트에서 nvcc --version 명령어 입력


## 5. cuDNN 설치
1. 자신이 설치한 CUDA Toolkit 버전과 호환되는 cuDNN 탐색 후 설치
	- Nvidia 계정 필요 
	- [cuDNN Archive](https://developer.nvidia.com/rdp/cudnn-archive)
	![[cuDNN.png]]
2. 설치한 파일의 압축을 풀고 폴더 안의 include, lib, bin 폴더 확인
3. cuDNN의 include, lib, bin 폴더 안의 파일들을 각각 CUDA Toolkit 폴더의 include, lib, bin 폴더 안으로 복사/붙여 넣기
	- CUDA Toolkit 폴더: C:/Program Files/NVIDIA GPU Computing Toolkit/CUDA/v12.x/


## 6. PyTorch 설치
1. PyTorch 설치를 원하는 환경에 pytorch를 설치
	- [PyTorch](https://pytorch.org/)

2. torch 작동 확인
	- torch를 import하고, cuda 사용 가능 여부와 torch version을 확인
```python
import torch

print(torch.__version__)
print(torch.version.cuda)
print(torch.cuda.is_available())  # True 인 경우면 설치 완료!
```
