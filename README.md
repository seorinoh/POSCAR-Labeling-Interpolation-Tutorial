# POSCAR Labeling & Interpolation-Tutorial
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
### Initial-POSCAR 파일 파싱 끝.
같은 방법으로**Final-POSCAR 파일** 파싱 해주면 된다. 
