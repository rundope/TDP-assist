# Phase 1 진행 기록: Absolute mass vertical slice

> 시작일 2026-10-08 · 관련 결정 D-008 (Accepted)

## 진행 방식 (사용자 지시)

각 단계는 **① 논의 → ② 결정 기록 → ③ 구현 → ④ 사용자 확인** 순서로 진행한다.
앞 단계가 확정되기 전에는 다음 단계를 구현하지 않는다.
결정은 [DECISIONS.md](../DECISIONS.md)에 기록하고, 이 문서에는 단계별 논의 요약을 남긴다.

## 결정 순서

| 순서 | 단계 | 이 단계에서 정할 것 | 상태 |
|---|---|---|---|
| 1 | 1-1 골격 | 언어, 스택, Phase 1 의존성을 정한다 (D-003). | **논의 중** |
| 2 | 1-2 chem | 질량 상수의 출처, neutral mass 규약, fragment ion 세트와 activation 매핑을 정한다. | 대기 |
| 3 | 1-3 io | 변환 설정(D-001), deconvolution 엔진·버전·파라미터(D-002), msalign 변형 처리 방식을 정한다. | 대기 |
| 4 | 1-4 db | FASTA proteoform 생성 규칙(initiator Met, N-acetyl, Cys 상태)과 ProForma 표기(D-004)를 정한다. | 대기 |
| 5 | 1-5 search/score | Precursor 창의 의미, isotope offset 범위, fragment count 규칙, P-score 파라미터화를 정한다. | 대기 |
| 6 | 1-6 report/CLI | TSV 컬럼, CLI 형태, 설정 파일 형식을 정한다. | 대기 |
| 7 | 1-7 검증 | 비교 지표와 합격 기준을 정한다. | 대기 (사용자 데이터 필요) |

**순서의 근거.** 의존 관계는 chem → (io, db) → search → report → 검증이다.
io와 db는 서로 독립이지만 io를 먼저 둔다. io 단계에서 처음으로 사용자의 실제 RAW 파일과 TopFD 설치가 필요하고, 준비에 걸리는 시간이 가장 길기 때문이다.

---

## Step 1 — 1-1 골격: 언어와 스택 (D-003)

### 논의

이 결정을 맨 앞에 두는 이유는 Phase 1의 결정 중 되돌리는 비용이 가장 크기 때문이다. 이후 모든 코드가 이 결정 위에 쌓인다.

경합한 후보는 세 가지다.

| 기준 | Python | C# (.NET) | Rust |
|---|---|---|---|
| 사용자 | Jupyter에서 중간 결과를 그래프로 바로 확인할 수 있다. | 타입 선언이 많아 읽기가 무겁다. | 진입 장벽이 가장 높다. |
| 구현 비용 | numpy, scipy, pyteomics, mokapot(Percolator 계열 rescoring)을 그대로 쓴다. | Thermo RawFileReader와 mzLib(MetaMorpheus 라이브러리)가 있다. | Sage 같은 선례가 있으나 직접 만들 부분이 많다. |
| 속도 | 순수 Python 루프는 느리다. numpy 벡터화와 numba로 해결한다. | 빠르다. | 가장 빠르다. |
| 배포 | Windows 일반 사용자용 설치가 번거롭다. 번들러가 필요하다. | Windows 데스크톱 앱을 만들기 쉽다. | 단일 실행 파일을 만들기 쉽다. |
| 전략 | 병목 kernel만 Rust(PyO3)로 교체할 수 있다. | Thermo, Agilent, SCIEX의 SDK가 .NET이어서 native reader를 만들 때 유리하다. | Python에서 PyO3로 불러 쓸 수 있다. |

**C#에 대한 가장 강한 논거**는 vendor SDK 3종이 .NET이라는 점이다.
그러나 D-001(변환은 외부 도구에 맡긴다)을 채택하면 이 장점은 Phase 6 전까지 쓰이지 않는다. Thermo RAW를 직접 읽어야 하는 상황이 와도 Python에서 pythonnet으로 .NET 라이브러리를 호출하는 경로가 있다.

**전제.** 사용자는 소프트웨어 개발자가 아니다(2026-10-08 사용자 확인). 그래서 판정 기준을 "사용자가 단계마다 결과를 직접 눈으로 확인할 수 있는가"에 두었다.

**C#이 이기는 조건**은 Windows 데스크톱 GUI가 Phase 1부터 반드시 필요한 경우 하나다. 처음에 두었던 "사용자가 C#에 익숙한 경우"라는 조건은 위 전제에 따라 해당하지 않는다.

### 권고안

- 언어는 Python 3.11 이상으로 한다. 3.11은 설정 파일(TOML)을 표준 라이브러리 `tomllib`로 읽을 수 있는 최저 버전이다.
- Phase 1의 실행 의존성은 **numpy 하나**로 제한한다.
  - Phase 1의 Absolute mode는 spectrum당 후보가 수 개 수준이라 numpy만으로 충분하다(DESIGN §5.4).
  - numba는 새 Python·numpy 버전을 늦게 따라가는 경우가 있다. 그래서 계산량이 실제로 커지는 Phase 2 Biomarker에서 도입한다.
  - FASTA reader는 직접 작성한다(수십 줄 규모). mzML과 UniProt XML 파서(pyteomics, lxml)는 필요해지는 Phase에서 도입한다.
- 개발 도구는 pyproject.toml 기반 src layout, pytest(테스트), ruff(코드 검사)로 한다.

### 결정

승인 대기 (D-003, Proposed).

### 구현 범위 (승인 후)

- `pyproject.toml`을 만든다.
- `src/tdp_assist/` 패키지 뼈대를 만든다. 하위 모듈은 DESIGN §4.2 구조를 따르며 빈 상태로 둔다.
- pytest와 ruff 설정을 넣는다.
- `tdp-assist --version`이 동작하는 최소 CLI를 만든다.
- 계산 코드는 넣지 않는다.

---

## Step 3 사전 논의 — deconvolution 엔진의 신뢰성 (사용자 제기, 2026-10-08)

Step 3에서 확정할 주제지만, 사용자가 먼저 제기해서 논의를 시작했다. 결정은 아직 하지 않았다.

### 사용자 입장

- TopFD를 믿을 수 있는지 의문이 있다.
- Xtract를 선호한다. Xtract가 불가능하면 THRASH 기반으로 하거나, THRASH와 TopFD의 장점만 뽑아 하이브리드로 재구성하는 방안을 원한다.

### 확인한 근거

1. **개발자 자체 벤치마크.** TopFD 논문(Basharat et al. 2023)은 7개 데이터셋에서 TopFD, ProMex, FLASHDeconv, Xtract를 비교했다. 유효 feature 비율은 TopFD·FLASHDeconv·Xtract가 비슷했고, 반복 측정 재현성은 TopFD가 가장 높았다고 보고했다. TopFD의 전신인 MS-Deconv 논문(Liu et al. 2010)도 THRASH와 Xtract보다 올바른 monoisotopic mass를 더 많이 찾았다고 보고했다. 두 결과 모두 개발자 자체 평가다.
2. **독립 비교 연구.** Tabb et al. 2023(J Proteome Res 22(7):2199–2217)은 Xtract, Bruker AutoMSn, Mascot Distiller, TopFD, FLASHDeconv를 Orbitrap과 Q-TOF 데이터에서 비교했다. Deconvolution 엔진마다 precursor charge와 질량 판정이 달랐고, 이것이 동정 결과의 차이로 이어졌다. 동정된 proteoform의 상당수가 네 파이프라인 중 하나에서만 나왔고, 저자들은 실험마다 최소 두 가지 검색 파이프라인을 쓰라고 권고했다.
3. **Xtract의 한계 보고.** FLASHDeconv 논문(Jeong et al. 2020)은 Xtract와 ProMex가 20–100 kDa 구간에서 feature를 거의 보고하지 못했다고 적었다. 이 구간은 isotope가 분리되지 않는 경우가 많다.
4. **THRASH 공개 구현.** THRASH는 PNNL의 DeconTools(Decon2LS, C#, 오픈소스)에 구현되어 있다(Jaitly et al. 2009). 현재 플랫폼 지원 범위는 확인하지 못했다.

### THRASH와 TopFD의 단계별 비교

| 처리 단계 | THRASH | TopFD | 판단 |
|---|---|---|---|
| 입력 | Profile 신호를 그대로 쓸 수 있다. | Centroid 입력을 기대한다. | THRASH가 원 신호 정보를 더 쓴다. |
| Charge 판정 | Patterson/Fourier 자기상관으로 정한다. | 여러 charge 후보를 만들어 비교한다. | TopFD가 harmonic 오류에 유리할 것으로 본다(추정). |
| Mono mass 결정 | Averagine 패턴을 least-squares로 맞춘다. | 패턴 유사도 점수와 머신러닝 평가를 쓴다. | 우열을 확인하지 못했다. |
| 겹친 envelope | 큰 peak부터 맞추고 빼는 greedy 방식이다. | 후보 전체에서 조합을 고르는 방식이다. | TopFD는 앞 단계 오류가 뒤로 번지지 않는다. |
| LC 방향 통합 | 없다(scan 단위). | 여러 MS1 scan과 charge state를 feature로 묶는다. | TopFD의 precursor 질량이 더 안정적이다. |
| 공개 | 알고리즘이 공개되어 있고 DeconTools에 구현되어 있다. | 코드가 공개되어 있다(Apache-2.0). | — |

두 방식의 장점을 합치면 "TopFD로 찾고, THRASH식 profile fit으로 다듬는" 구조가 된다. TopFD가 찾은 envelope마다 원래 profile 신호에 least-squares로 다시 맞춰 mono mass를 보정하고, 맞지 않는 envelope는 걸러내는 방식이다.

### 설계자 권고 (승인 대기)

- 하이브리드 방향에는 동의한다. 다만 **결과 수준 하이브리드**를 먼저 한다. 여러 엔진(TopFD, FLASHDeconv, 가능하면 Xtract)의 결과를 모으고, precursor 질량이 엇갈리면 모두 후보로 넘겨 fragment 증거로 판정한다.
- **코드 수준 하이브리드**(THRASH식 profile re-fit, THRASH 자체 구현)는 벤치마크가 필요성을 보여줄 때 착수한다.
- Xtract는 구현할 수 없지만 결과를 읽어 오는 것은 가능하다. 예전 ProSightPC cRAWler의 PUF, FreeStyle이나 BioPharma Finder의 Xtract 결과가 그 경로다.
- Step 3을 3a(엔진 벤치마크)와 3b(결과 수준 하이브리드)로 나눈다.

