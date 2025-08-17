# POSCAR Labeling & Interpolation-Tutorial
이 튜토리얼은 **grenier phase**에서 강유전성 물질(ferroelectric material)이 외부 전기장에 의해 분극 전환(switching) 되는 과정에서 넘는       
에너지 장벽(**energy barrier**)를 계산하기 위한 중간 과정(**interpolated structures**)을 만드는 코드에 대한 내용이다.

---

## 📚 참고 논문
아래의 논문을 통해 ferroelectric 및 Grenier phase에 대한 이론적인 내용을 더 자세하게 볼 수 있다.
- **Yongjin Shin** & Giulia Galli, *Tunable ferroelectricity in oxygen-deficient perovskites with Grenier structure* (2023)  
https://www.nature.com/articles/s41524-023-01175-5

---  
## 💻 프로그램 set-up
코드 설명을 들어가기에 앞서, 이 튜토리얼은 **Python 3.13.5** 환경에서 작성 하였다.  
코드 작성의 주된 프로그램은 **VS Code** 에디터를 사용하였고, 중간 과정을 확인하기 위해 spyder 프로그램도 사용하였다.  
.  
.  
**실습에 필요한 프로그램을 설치하도록 하자.**  
**1. Anaconda 프로그램 설치**  
(https://www.anaconda.com/download)  

<img width="450" height="350" alt="image" src="https://github.com/user-attachments/assets/20763919-be62-4696-93c1-dd0605b4e810" />
<img width="550" height="550" alt="image" src="https://github.com/user-attachments/assets/001ef77e-d2d2-4007-a265-ecbf1b737659" /> <br><br>
aconda Navigator를 실행하면 실습에 필요한 VS Code와 Spyder 프로그램을 확인할 수 있다.<br><br>

**2. vesta 프로그램 설치**  
(https://jp-minerals.org/vesta/en/download.html) 

**3. Sublime Text 프로그램 설치**  
(https://www.sublimetext.com/)



초기 구조 POSCAR 파일은 Nd, Ca, Fe, O로 이루어진 Pmma 공간군 POSCAR file을 사용하였고, 
최종 구조 POSCAR 파일은 Nd, Ca, Fe, O로 이루어진 Pmc21 공간군 POSCAR file을 사용하였다.

#ㄴ뎃1
