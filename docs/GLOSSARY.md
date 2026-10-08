# 용어·약어

| 용어 | 풀네임 | 설명 |
|---|---|---|
| Top-down proteomics | — | 단백질을 효소로 자르지 않고 intact 상태로 측정해서 proteoform 단위로 동정하는 방법이다. |
| Proteoform | — | 하나의 유전자에서 나온 단백질이 서열 변이, 절단, PTM 조합에 따라 갖는 구체적인 분자 형태 하나를 말한다. |
| PTM | Post-Translational Modification | 번역 후 수식이다. 예: phosphorylation, acetylation. |
| PrSM | Proteoform-Spectrum Match | MS2 spectrum 하나와 proteoform 후보 하나의 매치를 말한다. Bottom-up의 PSM에 해당한다. |
| MS1 / MS2 | — | MS1은 intact precursor를 측정한 spectrum이고, MS2는 precursor를 조각내서 얻은 fragment spectrum이다. |
| Deconvolution | — | 여러 charge state와 isotope peak으로 흩어진 신호를 하나의 neutral mass로 환산하는 과정이다. |
| Monoisotopic mass | — | 모든 원자가 가장 가벼운 동위원소일 때의 질량이다. |
| Average mass | — | 동위원소 자연 존재비로 가중평균한 질량이다. Isotope peak이 분리되지 않을 때 쓴다. |
| Averagine | — | 단백질의 평균 원소 조성을 가진 가상의 아미노산이다. Isotope 분포를 예측하는 데 쓴다. |
| Off-by-one 오류 | — | Deconvolution이 monoisotopic peak을 1 Da 옆 isotope peak으로 잘못 고르는 오류다. |
| ppm | parts per million | 질량 오차의 상대 단위다. 10 ppm은 10 kDa에서 0.1 Da다. |
| mzML | — | HUPO-PSI의 질량분석 데이터 표준 XML 포맷이다. |
| msalign | — | TopFD와 FLASHDeconv가 출력하는 deconvoluted spectrum 텍스트 포맷이다. |
| PUF | ProSight Upload Format | ProSightPC가 deconvolution 결과를 담던 파일 포맷이다. |
| PEFF | PSI Extended FASTA Format | Annotation(변이, 수식)을 담을 수 있게 확장한 FASTA 표준이다. |
| ProForma | — | Proteoform을 문자열로 표기하는 HUPO-PSI 표준 표기법이다(현재 2.0). |
| HUPO-PSI | Human Proteome Organization – Proteomics Standards Initiative | 프로테오믹스 데이터 표준을 정하는 국제 조직이다. |
| FDR | False Discovery Rate | 채택한 결과 중 틀린 결과의 비율 추정치다. |
| TDA / TDC | Target-Decoy Approach / Competition | 가짜 서열(decoy)을 섞어 검색해서 FDR을 추정하는 방법이다. |
| q-value | — | 해당 결과를 채택하기 위해 감수해야 하는 최소 FDR이다. |
| P-score | — | ProSight의 점수다. 매치가 우연히 그만큼 좋을 확률(Poisson 모델)이다. |
| E-value | Expectation value | P-score × 후보 수로, 우연히 그만큼 좋은 매치가 나올 기대 개수다. |
| C-score | Characterization score | Proteoform이 얼마나 완전하게 특성화되었는지(PTM 위치 포함)를 나타내는 Bayesian 점수다. |
| Δm mode | Delta mass mode | 설명되지 않는 질량차 하나를 허용하고, 그 위치를 fragment로 추정하는 ProSight의 검색 옵션이다. |
| Search tree | — | 여러 검색을 순서대로 연결해서 앞 단계에서 동정되지 않은 spectrum을 다음 단계로 넘기는 구성이다. |
| Chimeric spectrum | — | Isolation window에 여러 precursor가 함께 들어가 fragment가 섞인 spectrum이다. |
| CID / HCD | Collision-Induced Dissociation / Higher-energy Collisional Dissociation | 충돌로 조각내는 방식이다. 주로 b/y ion이 생긴다. |
| ETD / ECD | Electron Transfer / Electron Capture Dissociation | 전자 전달·포획으로 조각내는 방식이다. 주로 c/z• ion이 생긴다. |
| EThcD | Electron-Transfer/Higher-energy Collision Dissociation | ETD와 HCD를 결합한 방식이다. b/c/y/z• ion이 생긴다. |
| EAD | Electron Activated Dissociation | SCIEX ZenoTOF의 전자 기반 fragmentation이다. 주로 c/z• ion이 생긴다. |
| UVPD | Ultraviolet Photodissociation | 자외선으로 조각내는 방식이다. a/b/c/x/y/z 계열 ion이 모두 생긴다. |
| Q-TOF | Quadrupole Time-of-Flight | Agilent, SCIEX, Bruker 등이 쓰는 질량분석기 유형이다. |
| FT-ICR | Fourier Transform Ion Cyclotron Resonance | Bruker solariX 등 초고분해능 질량분석기다. |
| SDK | Software Development Kit | Vendor가 제공하는 파일 판독 라이브러리다. |
| ADR | Architecture Decision Record | 설계 결정과 근거를 기록하는 문서 형식이다. |
| numba | — | Python 함수를 기계어로 컴파일해 반복 루프를 빠르게 만드는 라이브러리다. |
| MetAP | Methionine Aminopeptidase | 새로 합성된 단백질의 N 말단 Met을 제거하는 효소다. |
