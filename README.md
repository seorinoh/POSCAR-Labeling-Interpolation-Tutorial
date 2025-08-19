# POSCAR Atom Labeling & Interpolation - Tutorial
이 튜토리얼은 **grenier phase**에서 강유전성 물질(ferroelectric material)이 외부 전기장에 의해 분극 전환(switching) 되는 과정에서 넘는       
에너지 장벽(**energy barrier**)를 계산하기 위한 중간 과정(**interpolated structures**)을 만드는 코드에 대한 내용이다.
<br> <br>

---

## 📚 참고 논문
아래의 논문을 통해 ferroelectric 및 Grenier phase에 대한 이론적인 내용을 더 자세하게 볼 수 있다.
- **Yongjin Shin** & Giulia Galli, *Tunable ferroelectricity in oxygen-deficient perovskites with Grenier structure* (2023)  
https://www.nature.com/articles/s41524-023-01175-5
<br> <br>

---  
## 💻 프로그램 set-up
코드 설명을 들어가기에 앞서, 이 튜토리얼은 **Python 3.13.5** 환경에서 작성 하였다.  
코드 작성의 주된 프로그램은 **VS Code** 에디터를 사용하였고, 중간 과정을 확인하기 위해 spyder 프로그램도 사용하였다.  
.  
.  
### 실습에 필요한 프로그램을 설치하도록 하자.  

### 1. Anaconda 프로그램 설치 (https://www.anaconda.com/download)  

<img width="450" height="350" alt="image" src="https://github.com/user-attachments/assets/20763919-be62-4696-93c1-dd0605b4e810" />
<img width="550" height="550" alt="image" src="https://github.com/user-attachments/assets/001ef77e-d2d2-4007-a265-ecbf1b737659" /> <br>
aconda Navigator를 실행하면 실습에 필요한 VS Code와 Spyder 프로그램을 확인할 수 있다.<br><br><br>

### 2. vesta 프로그램 설치 (https://jp-minerals.org/vesta/en/download.html)  
<img width="500" height="350" alt="image" src="https://github.com/user-attachments/assets/7b132c57-c852-4ead-89d1-f542c8143a1b" /> <br>
이 프로그램을 통해 POSCAR file 원자 구조를 3차원으로 시각화 할 수 있다. (글씨가 깨진다면 다른 웹브라우저 사용을 권장한다.)<br><br><br>

### 3. Sublime Text 프로그램 설치 (https://www.sublimetext.com/)  
<img width="500" height="350" alt="image" src="https://github.com/user-attachments/assets/e40acd48-e7ec-4c15-8733-a002f4798230" /> <br>
메모장과 유사한 프로그램이라고 생각하면 된다.<br><br><br>

### 4. 추가적인 모듈 설치  
코드를 실행하기 위한 추가적인 numpy 모듈을 설치해야한다.<br>   
<img width="508" height="110" alt="image" src="https://github.com/user-attachments/assets/ad544604-de31-49f9-a13f-a176e32014b1" />

Anaconda Prompt를 실행하고 `pip install numpy`을 터미널 창에 입력하여 모듈을 설치해준다.
<br> <br> <br>

---

## 📁 POSCAR 파일 형식 설명
코드를 설명하기 전에, 프로그래밍의 기반이 되는 POSCAR 파일의 구성에 대해 설명하겠다.  
```
(1)    Nd Ca Fe  O                                                    # 구조 이름(원소 이름)
(2)    1.0                                                            # 스케일 팩터
(3)    5.5798778534         0.0000000000         0.0000000000         # 라티스 벡터 a축
(4)    0.0000000000        22.6841869354         0.0000000000         # 라티스 벡터 b축
(5)    0.0000000000         0.0000000000         5.5896039009         # 라티스 벡터 c축
(6)    Nd   Ca   Fe    O                                              # 원소 이름
(7)    4    8   12   32                                               # 원소별 개수
(8)    Direct                                                         # 원소 좌표 형식
(9)    0.250000000        -0.000000000         0.232330652            # 원소 좌표 - Nd 1번 (a, b, c) 
(10)   0.250000000         0.500000000         0.232330652            # 원소 좌표 - Nd 2번
(11)   0.750000000        -0.000000000         0.767669327            # 원소 좌표 - Nd 3번
(12)   0.750000000         0.500000000         0.767669327            # 원소 좌표 - Nd 4번
(13)   0.250000000         0.338453300         0.265420356            # 원소 좌표 - Ca 1번
(14)   0.250000000         0.838453089         0.265422275                      
(15)   0.750000000         0.161546932         0.734577725                      .
(16)   0.750000000         0.661546700         0.734579687                      .
(17)   0.250000000         0.161546711         0.265420356                      .
(18)   0.250000000         0.661546953         0.265422275                      .
(19)   0.750000000         0.338453047         0.734577725
(20)   0.750000000         0.838453258         0.734579687            # 원소 좌표 - Ca 8번
(21)   0.250000000         0.086420305         0.746812749            # 원소 좌표 - Fe 1번
(22)   0.250000000         0.586417383         0.746808995                      .
(23)   0.750000000         0.413582659         0.253190962                      .
(24)   0.750000000         0.913579674         0.253187272                      .
```

### 추가 설명  
**(3)~(5)** 라티스 벡터(lattice vector)란 하나의 단위격자(unit cell)를 정의하는 세 축 방향의 벡터이다.
## 
**(8)** 은 원소 좌표의 형식을 적는 줄이고, 좌표 형식은 Cartesian와 Direct가 있다. 

 - **Cartesian** 형식은 실제 공간(Å 단위)에서 (a, b, c) 좌표로 원자의 위치를 표현한 것이다.  
    즉, Cartesian은 **실제 물리적 거리 단위의 좌표**로 나타내는 형식이다.  

 - **Direct** 형식은 단위 격자(lattice)의 세 개 벡터를 기준으로 0~1 사이의 비율로 원자의 위치를 표현한 것이다.  
    즉, 원소의 좌표를 실제 거리로 나타낸 것이 아니라 **상대적인 비율로 나타낸 좌표 형식**이라고 생각하면 된다.<br><br>

따라서, **Direct** 형식은 단위 셀 내부의 상대 좌표(0~1 범위)이기 때문에 **실제 원소의 공간적 위치**를 얻으려면,   
POSCAR 파일의 **원소 좌표 값**에 **라티스 벡터를 행렬 곱**해주어야 한다. (잘 기억해 두자.)
<br><br>

---

## 📌 코드 설명
### 📍1. Parsing POSCAR Files
초기 구조 POSCAR 파일은 Nd, Ca, Fe, O로 이루어진 Pmma 공간군 POSCAR file을 사용하였고,  
최종 구조 POSCAR 파일은 Nd, Ca, Fe, O로 이루어진 Pmc21 공간군 POSCAR file을 사용하였다.  
<br><br>
```
# 현재 디렉터리 위치 확인

%pwd
```
- `pwd`: 현재 작업 중인 디렉터리 위치 확인
  
내가 원하는 파일 안에서 python 파일이 열려 있는 지 확인한다.  
<br><br>
```
# 디렉터리 내 파일 목록 확인

import os
file_list = os.listdir()
file_list
```
- `os.listdir()`: 현재 디렉터리 안에 있는 파일들의 목록을 확인
  
python 파일과 interpolation 하기 위한 POSCAR 파일이 들어있어야 한다.  
<br><br>
```
# .vasp로 끝나는 파일들만 poscar_files로 지정하고 확인

import glob
poscar_files = glob.glob("./*.vasp")
poscar_files
```
- `glob.glob("./*.vasp")`: **.vasp**로 끝나는 파일들만 불러옴

POSCAR 파일들만 리스트로 묶여있는지 확인한다.  
<br><br>
```
# poscar_files들 중 initial_switching 파일을 지정

poscar_initial = poscar_files[0]
print(poscar_initial)
```
- `poscar_files` 리스트에서 initial_switching으로 쓸 POSCAR 파일을 지정해서 확인한다.  
<br><br>
```
# poscar 파일 내용을 줄마다 정의하기 위해 읽기 모드로 열기

read_initial_lines = open(poscar_initial, 'r')
initial_lines = read_initial_lines.readlines()
```
- `.readlines()`: POSCAR 파일의 각 줄을 리스트의 요소로 `initial_line`에 저장한다.  
<br><br>

**POSCAR 파일 각 줄별로 파싱하기.** 
<br>

```
# 구조 이름

initial_title = initial_lines[0].strip()
```
- (`initial_lines`의 0번째 요소 = POSCAR 파일의 첫번째 줄)을 초기POSCAR 파일의 제목으로 지정
- `.strip()`: '/n'과 같은 불필요 요소들 제거 역할  
<br><br>
```
# 스케일 팩터

initial_scale = float(initial_lines[1].strip())
```
- `initial_lines`의 1번째 요소를 `float`로 감싸서 스케일 팩터를 실수화 해준다.
- (연산을 위해 숫자는 모두 float, int화 해주는 것이 좋다.)
<br><br><br>  
```
# 3x3 라티스 벡터
# 한 줄씩, 한 줄당 3개의 좌표를 한 개씩 쪼개서 문자를 실수화(float)

initial_lattice = []  

for i in range(2,5):
    lattice_row_list = initial_lines[i].split()       # lattice_row_list = [a,b,c] 리스트

    lattice_row = []
    for part in lattice_row_list:
        lattice_row.append(float(part))               # a,b,c 좌표 하나씩 실수화 해서 [a,b,c] 리스트로 만들기 

    initial_lattice.append(lattice_row)               # [a,b,c]/[d,e,f]/[g,h,i] 3개 리스트를 하나의 리스트로 묶음  

print(initial_lattice[0])                             # 실수화 된 a축 라티스 벡터 리스트
print(initial_lattice[1])
print(initial_lattice[2])
```  
- `float`는 리스트 전체를 실수화 시키지 못한다. (즉, lattice 벡터 한 줄씩 실수화(float) 시킬 수 없음)  
   따라서 3x3 라티스 벡터를 한 줄씩, 좌표 한 개씩 쪼개서 실수화 해준다.
  
- `.split()`: 공백을 기준으로 나누기 때문에 [a b c] -> [a, b, c]로 바꿔준다.
- `.append()`: 각 좌표들을 덧붙이면서 리스트로 만들어 준다.
<br><br><br>
```
# 각 원자의 종류

initial_atom_types = initial_lines[5].split()
```
<br><br>
```
# 각 원자 종류별 개수  

initial_atom_counts = []

initial_atom_counts_list = initial_lines[6].split()    # 공백 기준으로 원자 수 나누고 list로 저장
for part in initial_atom_counts_list:
    initial_atom_counts.append(int(part))              # 원자 수 각각 정수화 시키고 list로 저장

print(initial_atom_counts)
```
<br><br>
```
# 원자들 총 개수

initial_atoms_total = sum(initial_atom_counts)

print(initial_atoms_total)
```
- `int`로 정수화 된 각 원자들 개수를 더해 `sum`으로 총합을 구한다.  
<br><br>
```
# 원자 좌표의 형식

initial_coord_type = initial_lines[7].strip()

print(initial_coord_type)
```
<br>

```
# 각 atom들 좌표

initial_coords = []  

for i in range(8,64):
    coords_row_list = initial_lines[i].split()           #coords_row_list = [a,b,c] 좌표 리스트
    coords_row = []
    for part in coords_row_list:
        coords_row.append(float(part))                   # a,b,c 좌표 하나씩 실수화 해서 [a,b,c] 리스트로 만들기<br> 
    initial_coords.append(coords_row)                    # [a,b,c]/[d,e,f]/... 56개 리스트를 하나의 리스트로 묶음 <br> 
print(initial_coords[0])                                 # 실수화 된 Nd 1번 좌표 리스트
```
- lattice vector와 같은 방식으로 3 x 56의 원자 좌표를 한 줄씩, 좌표 한 개씩 쪼개서 실수화 해준다.
<br><br>
### initial_POSCAR 파일 파싱 끝.
같은 방법으로 **final_POSCAR 파일**도 파싱 해주면 된다.  
(https://github.com/seorinoh/POSCAR-Labeling-Interpolation-Tutorial/blob/main/poscar_final%20parsing)  
<br><br><br>
##
### 📍2. Atom Labeling  
비교하는 두 개의 POSCAR 파일이 서로 **종류가 같은 원자**끼리, **같은 번호**로 원자들 좌표가 정렬되어 있는 지는 알 수 없다. 

따라서 initial과 final 원자들의 **거리를 하나하나 비교**해 final_POSCAR 파일의 **원자 좌표를 재정렬** 하는 코드가 필요하다.  
<br>  
아래의 코드는 처음 만든 원자 라벨링 코드이다.  
(https://github.com/seorinoh/POSCAR-Labeling-Interpolation-Tutorial/blob/main/mis_atom_labeling%20code) <br>  
위의 코드로는 원자 라벨링에 **오류**가 난 것을 확인할 수 있었다.  
<br><br><br>
spyder 프로그램을 통해 코드를 줄 별로 디버그 하면서 찾은 **두 가지** 오류의 원인은 다음과 같다.  

#### (1) 원자의 pbc(periodic boundary conditions) 문제
#### (2) 원자 좌표가 Direct 형식으로 실제 거리를 반영하지 않는다는 문제
<br><br>
이제 각 문제들이 왜 발생하는 지, 어떻게 해결했고, 최종적인 코드는 무엇인지 설명하겠다.  
<br><br>
#### 🟥 (1) 원자의 pbc(periodic boundary conditions) 문제  
먼저, POSCAR 파일은 가장 작은 단위의 **구조 하나**에 대한 정보를 담고 있는 파일이다.  
(VESTA로 POSCAR 파일을 시각화 해보면 직육면체의 **하나의 구조**와 **이 구조 안에 있는 원자들의 위치**를 확인 할 수 있다.)  
<img width="150" height="500" alt="image" src="https://github.com/user-attachments/assets/d35f3a17-5709-49ee-99a4-4f9e11640235" />
<br>   
즉, POSCAR 파일에는 하나의 **구조 안**에 있는 **원자들의 좌표**가 저장되어 있다. 

하지만 구조가 여러 개 모여 물질이 되기 때문에, 사실 구조는 **주기적으로 반복**되어 있다.  
<br><br>
(이해를 돕기 위해 2차원 정사각형 구조를 통해 **pbc**에 대해 더 설명하겠다.)  

<img width="363" height="362" alt="image" src="https://github.com/user-attachments/assets/5ec56d5f-884a-46e3-b0aa-82991a5fbc47" />   

- 각 정사각형이 하나의 구조(cell)이라고 가정한다.
- 5번 cell의 정보를 POSCAR 파일로 가지고 있다고 하자.
- 5번을 제외한 cell은 5번과 같은 구조가 똑같이 반복된 것이다.
- 파란색 원이 initial 상태의 원자 1번이라고 하자.
- 빨간색 원이 final 상태의 원자 1번이라고 하자.
- 원자 1번이 switching 되면서 왼쪽으로 조금 이동하였다고 하자. 
<br> 
5번 cell에 있던 원자 1번은 switching 후 4번 cell로 이동하였음을 알 수 있다.

동시에, 6번 cell에 있던 원자 1번은 switching 후 5번 cell로 넘어왔음을 알 수 있다.  
<br><br> 
이렇게 된다면, **final_POSCAR 파일**에는 initial_POSCAR 파일에 있던 5번 cell의 원자 1번이 아니라 **6번 cell의 원자 1번의 좌표**가 저장된다.  
이것이 **pbc(periodic boundary conditions) 문제**이다.

**동일한 구조가 반복됨** + **POSCAR 파일은 하나의 구조 안에 있는 원자에 대한 좌표를 저장함**으로 인해,  
initial_POSCAR와 final_POSCAR **같은 원자의 좌표 차이**가 많이 나면서 원자 라벨링에 오류를 일으키는 것이다.    
<br><br>
#### 🟦 (1) Solution
이 문제를 **해결**하기 위해서는 **좌표 차이**를 구해 그 차이가 **0.5보다 크면** 좌표에 **-1**을 해주어 같은 cell에 있던 원자의 위치를 찾아주어야 한다.  

(이해를 돕기 위해 2차원 정사각형 구조를 통해 더 설명하겠다.)  

<img width="1303" height="366" alt="image" src="https://github.com/user-attachments/assets/203908b5-dae4-425f-b070-452501efe6c5" />  
<br><br>  

- initial_POSCAR 파일과 final_POSCAR 파일의 **원자 좌표 차이**를 구한다.  
- 이 좌표 차이의 절댓값이 0.5보다 크다면 다른 cell의 원자이기 때문에 좌표 차이를 **반올림**해서 빼주어 같은 cell에 있던 원자 거리를 구할 수 있다.
<br><br><br><br>

#### 🟥 (2) 원자 좌표가 Direct 형식으로 실제 거리를 반영하지 않는다는 문제  
위에서 POSCAR 파일의 (8)줄은 원소 좌표의 형식이라고 했었다. 
  
이 좌표의 형식이 Direct라면 구조의 실제 거리를 나타내지 못한다.

<img width="450" height="520" alt="image" src="https://github.com/user-attachments/assets/7cb8a9cc-0af4-47fc-be4d-bc4c5ed3544a" />

위와 같이 **vesta**는 실제 거리를 반영하기 때문에 직육면체의 구조로 표현되고, **실제 원자의 공간적 위치**를 나타낸다.    
하지만, **POSCAR 파일 원자의 좌표**는 모든 축에 대해 **1.0 스케일로** 표현하기 때문에 오른쪽 그림과 같이 원자의 좌표가 설정되어 있다고 생각하면 된다.
<br><br><br>
#### 🟦 (2) Solution
이 문제를 **해결**하기 위해서는 **원자의 좌표 차이**에 **라티스 벡터(3x3) 행렬 곱**을 해주면 된다.  
라티스 벡터를 곱해주면서 **실제 원소의 공간적 위치**를 얻을 수 있게 된다.
<br><br><br><br>

##
### ▪️원자 labeling 코드  
##
<img width="390" height="515" alt="image" src="https://github.com/user-attachments/assets/07d343dd-ada5-48d8-a42f-f072ec866280" />  

**initial 원자 하나**를 기준으로 **final 모든 원자**와 각각 거리를 구해서 **가장 작은 거리**의 final 원자를  
**같은 종류, 번호의 원자로** 지정해 원자 라벨링(reordered) 하는 코드를 만들었다.
<br><br>
```
# 연산을 위한 math 모듈, numpy 모듈

from math import sqrt
import numpy as np
```  
<br>  

```  
# 재정렬된 코드/ 측정된 거리 넣을 빈 리스트  

reordered_coords = []
measured_distance = []
```
<br>  

```
for base_atom_num in range(initial_atoms_total):
``` 
- `base_atom_num`: 비교 기준이 되는 initial 원자 번호
<br><br>

```
# initial 원자 한 개를 기준으로 final 56개의 원자와의 거리 넣을 빈 리스트
    row_measured_distance = []  

    for target_atom_num in range(final_atoms_total):
```
- `target_atom_num`: 비교 대상이 되는 final 원자 번호
<br><br>

```
# pbc 계산 후 같은 구조의 initial과 final 원자의 좌표 차이 넣을 빈 리스트
        new_distance_difference = []  

        for i_c, f_c in zip(initial_coords[base_atom_num], final_coords[target_atom_num]):
            distance_difference = np.subtract (i_c, f_c)
            difference_round = np.round(distance_difference)
            new_distance_difference.append(distance_difference - difference_round)
```
- `i_c, f_c`: 각각 initial 원자 좌표 리스트와 final 원자 좌표 리스트에서 좌표 1개씩 받음
- ` distance_difference`: intial 원자 좌표 - final 원자 좌표
- `difference_round`: 좌표 차이를 반올림
- `new_distance_difference`: 좌표 차이 - 반올림 값 = **pbc 고려된 좌표 차이**/ [a, b, c]형식
<br><br>

```
        new_distance_difference = np.array(new_distance_difference)
        lattice = np.array(initial_lattice)
        real_distance = new_distance_difference @ lattice
```
- `np.array(new_distance_difference)`: 좌표 차이 리스트를 행렬 벡터로 변환
- `np.array(initial_lattice)`: 라티스 벡터 리스트를 행렬 벡터로 변환
- `real_distance`: 좌표 차이와 라티스 벡터를 행렬 곱해 **실제 거리 차이** 계산/ [a, b, c]형식
<br><br>

```  
        total = real_distance[0]**2 + real_distance[1]**2 + real_distance[2]**2
        distance = sqrt(total)
        row_measured_distance.append(distance)
        
    measured_distance.append(row_measured_distance)
```
- `total`: 각 좌표 차이 제곱의 합
- `distance`: 루트 계산해서 거리 공식 계산 완료
- `row_measured_distance`: initial 원자 1개에 대한 final 원자 56개의 실제 거리 리스트로 저장
- `measured_distance`: 모든 initial 원자(56개)와의 실제 거리 리스트로 저장 = 리스트(56개의 요소) 속 리스트(56개의 요소)
<br>

```
[[1, 2, ..., 56], [1', 2', ..., 56'], ... , [1*, 2*, ..., 56*]]   #리스트 속 리스트
```
<br><br>  

```
# 56개의 거리 중 최소 거리를 같은 원자로 라벨링

for a in range (final_atoms_total):
    atom_distance = measured_distance[a]
    min_distance = min(atom_distance)
    min_distance_index = atom_distance.index(min_distance)
    min_index = int(min_distance_index)
    reordered_coords.append(final_coords[min_index])
```
- `atom_distance`: initial 원자 하나에 대한 final 원자 56개와의 거리 리스트
- `min(atom_distance)`: 56개의 요소 중 가장 작은 값 구하기
- `atom_distance.index(min_distance)`: 가장 작은 값의 인덱스 구하기
- `int(min_distance_index)`: 인덱스를 정수화
- `.append(final_coords[min_index])`: 거리가 가장 작았던 순서를 final 원자 좌표에서 꺼내 원자 재정렬
<br><br>


### Atom Labeling 끝.
  
##
### 📍3. reordered_POSCAR 파일 저장

```
# labeling해서 재정렬 된 파일 저장  

filename = f"1_pmc21_reordered.vasp"
with open(filename, 'w') as f:
```
- 파일 이름 '1_pmc21_reordered.vasp'으로 지정
- 파일을 쓰기 모드 `'W'`로 열고 이 파일을 `f`로 지정
<br><br>

```   
    # 원자 이름 적기
    f.write(f"{final_title}\n")  

    # 스케일 벡터 적기
    f.write(f"{final_scale}\n")

    # 라티스 벡터 한줄씩 적기
    for vec in final_lattice:
            f.write("  " + "  ".join(f"{x:.10f}" for x in vec) + "\n")

    # 원자 종류 적기
    f.write("  " + "  ".join(final_atom_types) + "\n")

    # 원자 종류별 갯수 적기
    f.write("  " + "  ".join(str(count) for count in final_atom_counts) + "\n")

    # 좌표 형식 적기
    f.write(f"{final_coord_type.capitalize()}\n")

    # 원자 좌표 한줄씩 적기
    for coord in reordered_coords:
        f.write("  " + "  ".join(f"{x:.10f}" for x in coord) + "\n")

    print("저장됨:", filename)
```
- 기존 POSCAR 파일과 동일한 형식으로 적어서 저장하기
<br><br>

```
# 저장 후, reordered_POSCAR 파일 확인해보기

filename = f"1_pmc21_reordered.vasp"
with open (filename, 'r') as f:
    content = f.read()

print(content)
```  

### reordered_POSCAR 파일 저장 끝.  
<br>  
  
##
### 📍4. Interpolation  
reordered_POSCAR 파일 저장까지 완료 했다면, 이제 **중간 과정**을 만들 준비가 되었다.  
initial & reordered POSCAR 파일에 저장된 라티스 벡터와 원자 좌표를 **선형 보간법(linear interpolation)** 을 사용해 중간 과정을 만들었다.  
<br>  
#### 선형 보간법(linear interpolation)이란?  

외부 전기장에 의해 switching 되는 중간 과정을 만들 때 쓰는 방법으로,   
initial과 final의 좌표를 알고 있을 때 **두 좌표 사이를 직선**으로 연결해 원하는 중간 과정 개수만큼 **좌표를 등간격**으로 나누는 것이다.  
<br>  
3차원에서 원자 1개에 대해 **linear interpolation**을 표현하면 아래의 그림과 같다.  
<img width="377" height="405" alt="image" src="https://github.com/user-attachments/assets/a4979f8e-d9e8-4bad-8a97-9466ae183cb9" />   
이때 interpolation으로 얻은 각 원자의 좌표 정보를 **새로운 interpolation_POSCAR 파일**로 저장 하는 것이 **중간 과정**을 만드는 것이다.  
<br><br><br><br>

##
### ▪️중간 과정 interpolation 코드  
##
```
# (interpolation)선형 보간하는 함수 정의  

# 선형 보간된 구조 lattice vector와 atom 좌표 
interpolated_structures = []

# n개의 중간 과정 만들기  

n_steps = 3      
for step in range(1, n_steps + 1):
```
- `n_steps`: 중간 과정 개수
- `step`: 중간 과정 위치 (1부터 n_steps까지)
<br><br>

```   
    # 보간 상수 x (중간 과정 위치 나타냄)
    x = step / (n_steps + 1)
```
- `x`: 보간상수/ x는 initial과 reorder 좌표 차이에 곱해주는 비율  
  (ex: 중간 과정을 3개로 만들면, 처음 x = 0/ 중간과정 1번 x = 0.25/ 중간과정 2번 x = 0.5/ 중간과정 3번 x = 0.75/ 끝 x = 1)

- 보간 공식: interpol = (초기 좌표) + **x**(보간상수) *(좌표 차이)
<br><br>

```
    # lattice vector interpolation 

    # 라티스 벡터 보간 후 중간 과정 벡터 저장할 곳
    intermediate_lattice = []  

    # 3x3 라티스 벡터를 한 줄씩 지정
    for vec_initial, vec_final in zip(initial_lattice, final_lattice):  

        # 좌표 하나씩 보간(interpolate) 
        interpol_vec = [i_v + x * (f_v - i_v) for i_v, f_v in zip(vec_initial, vec_final)]

        # 중간 과정 lattice vector 저장
        intermediate_lattice.append(interpol_vec)  



    # atom coords interpolation

    # 원자 좌표 보간 후 중간 과정 벡터 저장할 곳
    intermediate_coords = []  

    # 원자 좌표를 한줄씩 지정  
    for row_initial, row_reordered in zip(initial_coords, reordered_coords):

        # 좌표 하나씩 보간(interpolate)
        interpol_coords = [i_c + x * (f_c - i_c) for i_c, f_c in zip(row_initial, row_reordered)]  

        # 중간 과정 atom coords 저장  
        intermediate_coords.append(interpol_coords)  



    # 선형 보간된 구조 lattice vector와 atom 좌표로 저장
    interpolated_structures.append((intermediate_lattice, intermediate_coords))
```  

### 중간 과정 interpolation 코드 끝.  
<br>  
  
##
### 📍5. interpolation_POSCAR 파일 저장

```
# interpolation한 중간 과정 파일 저장

# interpolation 파일 이름 설정

for i in range (n_steps):
    filename = f"POSCAR_interpolated_{i+1}.vasp"
    intermediate_lattice, intermediate_coords = interpolated_structures[i]

 with open(filename, 'w') as f:
```
- `for`문을 사용해 initial에서 final 순서로 중간 과정 파일 이름을 'POSCAR_interpolated_{i+1}.vasp'으로 지정(1부터 n_steps까지)
- 파일을 쓰기 모드 `'W'`로 열고 이 파일을 `f`로 지정  
<br><br>

```   
        # 원자 이름 적기
        f.write(f"{initial_title}\n")

        # 스케일 벡터 적기
        f.write(f"{initial_scale}\n")

        # 라티스 벡터 한줄씩 적기
        for vec in intermediate_lattice:
                f.write("  " + "  ".join(f"{x:.10f}" for x in vec) + "\n")

        # 원자 종류 적기
        f.write("  " + "  ".join(initial_atom_types) + "\n")

        # 원자 종류별 갯수 적기
        f.write("  " + "  ".join(str(count) for count in initial_atom_counts) + "\n")

        # 좌표 형식 적기
        f.write(f"{initial_coord_type.capitalize()}\n")

        # 원자 좌표 한줄씩 적기
        for coord in intermediate_coords:
                f.write("  " + "  ".join(f"{x:.10f}" for x in coord) + "\n")

    print("저장됨:", filename)
```
- 기존 POSCAR 파일과 동일한 형식으로 적어서 저장하기
<br><br>

```
# 저장 후, POSCAR_interpolated_{1}.vasp 파일 확인해보기

filename = f"POSCAR_interpolated_{1}.vasp"
with open (filename, 'r') as f:
    content = f.read()

print(content)
```  

### interpolation_POSCAR 파일들 저장 끝.  
<br>  
  
##
### 📍6. VESTA로 'reordered' & 'interpolation' POSCAR 파일 구조 시각화 

코드 실행에 사용한 initial_POSCAR 파일과 final_POSCAR 파일은 서로 **원자 라벨링이 잘 되어있는** 상태였다.  
<br>  

먼저, 원자 라벨링이 **잘 되어있는 상태**에서 코드를 실행해 보았다.  

<img width="900" height="650" alt="image" src="https://github.com/user-attachments/assets/7826c3fb-5f9c-4ad2-9a7a-704f23c47af7" />  

- 실행 결과, reordered_POSCAR 파일의 경우 final_POSCAR 파일과 **원자의 라벨링이 동일**하게 되어 원자 라벨링이 잘 되었음을 확인하였다.  
<br>  

VESTA로 확인하였을 때도 원자의 라벨링이 잘 되었음을 확인 할 수 있다.  

<img width="200" height="500" alt="image" src="https://github.com/user-attachments/assets/f076bcd8-01c7-4b85-a11c-577b028f920a" />
<img width="200" height="500" alt="image" src="https://github.com/user-attachments/assets/920a69dd-6a78-4017-a3f6-d4f3250cac65" />  

- final_POSCAR 파일과 reordered_POSCAR 파일의 **원자 위치**와 **번호**가 **일치**하였다.
<br>    

##
이제 final_POSCAR 파일의 **원자 좌표**를 **임의로 2개만 바꿔서** 코드를 실행시켜 보고  

**원자 라벨링**이 잘 되는지, 이때의 reordered_POSCAR을 활용해 **중간 과정**은 잘 만들어지는 지 확인해 보았다.  
<br>  

<img width="900" height="550" alt="image" src="https://github.com/user-attachments/assets/c6cdd5f3-662d-4ec7-903a-92f0e02c51f7" />  
<br>  

- initial_POSCAR 파일을 기준으로 final_POSCAR 파일의 **Nd 2번 원자**와 **Fe 1번 원자**의 **좌표를 바꾸어** 저장하고 코드를 실행해보았다.

- 실행 결과 final_POSCAR 파일에서 바꿔놓은 두 원자의 좌표가 reordered_POSCAR 파일에서 **재정렬 되어있는 것**을 확인 하였다.  
<br><br>  

VESTA로 확인하였을 때도 원자의 라벨링이 잘 되었음을 확인 할 수 있다.  
<img width="450" height="530" alt="image" src="https://github.com/user-attachments/assets/8c3a6f2d-39d7-4572-838f-fa0bce5bf4d9" />  
<br><br>  

**중간 과정**은 잘 만들어졌는 지 확인해 보겠다.  

<img width="500" height="550" alt="image" src="https://github.com/user-attachments/assets/4ba4a346-fbf8-43b6-8dae-2de7b37fc846" />  

- initial_POSCAR 파일부터 중간 과정들 reordered_POSCAR 파일까지 a축의 라티스 벡터 좌표를 보았을 때 **선형 보간이 잘 되어있음**을 확인 하였다.  
<br><br>

VESTA로 확인하였을 때도 중간 과정이 잘 만들어진 것을 확인 할 수 있다.  
<br>  
<img width="900" height="550" alt="image" src="https://github.com/user-attachments/assets/546891df-aee7-4fb0-863c-637723d55f0f" />  

<br><br>
---
## ✒️ Tutorial 마무리

#### 지금까지 Grenier phase의 Ferroelectric materials의 switching 중간 과정을 POSCAR 파일로 만들기 위한 

#### 프로그램 설치 및 원자 라벨링 코드 작성 시 주의할 오류 원인 설명과 해결 방법 & 코드 설명에 대한 Tutorial을 마치겠습니다.




