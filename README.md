# POSCAR Labeling & Interpolation-Tutorial
이 튜토리얼은 grenier phase에서 강유전성 물질(ferroelectric material)이 외부 전기장에 의해 분극 전환(switching) 되는 과정에서 넘는       
에너지 장벽(energy barrier)를 계산하기 위한 중간 과정을 만드는 코드에 대한 내용이다.

아래의 Yongjin Shin & Giulia Galli 교수님 논문을 통해 ferroelectric 및 Grenier phase에 대한 내용을 더 자세하게 볼 수 있다.
https://www.nature.com/articles/s41524-023-01175-5



코드 설명을 들어가기에 앞서, 코드 작성은 VS code 프로그램을 사용하였다. 
초기 구조 POSCAR 파일은 Nd, Ca, Fe, O로 이루어진 Pmma 공간군 POSCAR file을 사용하였고, 
최종 구조 POSCAR 파일은 Nd, Ca, Fe, O로 이루어진 Pmc21 공간군 POSCAR file을 사용하였다.
