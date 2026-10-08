# 결정 로그 (Architecture Decision Record)

상태 값의 의미는 다음과 같다. `Proposed`는 설계자가 제안했고 사용자 승인을 기다리는 상태다. `Accepted`는 사용자가 승인한 상태다. `Rejected`는 기각된 상태이고, `Superseded`는 다른 결정으로 대체된 상태다.
근거의 상세 내용은 [design/DESIGN_v0.1.md](design/DESIGN_v0.1.md)의 해당 절에 있다.

| ID | 결정 | 근거 요약 | 관련 요구 | 상태 |
|---|---|---|---|---|
| D-001 | Vendor 파일은 직접 읽지 않는다. msconvert(4개 vendor)와 ThermoRawFileParser(Thermo)로 mzML(centroid)로 변환한 뒤 처리한다. | Reader 유지보수가 1종으로 줄고, Windows 전용 SDK 문제를 외부 도구가 해결하며, deconvolution 엔진이 mzML을 입력으로 받는다. (§3.3) | R-02, R-03 | Proposed |
| D-002 | Deconvolution은 외부 엔진을 adapter로 호출한다. 내부 계약은 msalign 포맷이다. 기본 엔진은 TopFD이고 FLASHDeconv를 두 번째로 붙이며, 최종 기본값은 Phase 1 비교 후 정한다. | 1차 검색은 deconvoluted mass로 해야 계산량이 100배 이상 줄어든다. 같은 msalign을 TopPIC에 넣어 교차 검증할 수 있다. (§3.2, §3.6) | R-03 | Proposed |
| D-003 | 언어는 Python 3.11+로 한다. Phase 1의 실행 의존성은 numpy 하나로 제한하고, numba는 Phase 2 Biomarker에서 도입한다. 계산 kernel은 인터페이스 뒤에 두고, 병목이 측정되면 그 kernel만 Rust로 교체한다. | 단계마다 중간 결과를 눈으로 확인하기 가장 쉽고, 과학 계산 생태계를 그대로 쓸 수 있다. 계산량 추정상 numba로 충분하다. C#의 장점(vendor SDK가 .NET)은 D-001 하에서 Phase 6까지 쓰이지 않는다. (§4.3, §5.4, [phase1/PHASE1_LOG.md](phase1/PHASE1_LOG.md) Step 1) | R-07 | Proposed (2026-10-08 개정) |
| D-004 | Proteoform 표기는 ProForma 2.0으로 통일한다. | HUPO-PSI 표준이므로 다른 도구와 결과를 주고받을 수 있다. (§4.4) | R-01 | Proposed |
| D-005 | 검색 모드는 하나의 proteoform 가설 모델의 preset으로 구현한다. Absolute, Δm, Biomarker를 먼저 만들고 Hybrid는 나중에 만든다. | 모드 추가가 설정 변경으로 끝나고, 절단과 수식이 함께 있는 proteoform으로 확장할 수 있다. (§5.5) | R-04, R-05, R-06 | Proposed |
| D-006 | P-score와 E-value는 ProSight 호환 점수로 유지한다. 합격선은 Phase 2부터 target-decoy FDR(tier별, PrSM/proteoform/protein 계층별)로 정한다. | E-value cutoff로는 실제 오류율을 알 수 없다. Search context를 무시하면 FDR이 20배 넘게 틀릴 수 있다. (§5.6, §5.7) | R-04, R-05, R-06 | Proposed |
| D-007 | 결과는 TSV와 SQLite로 저장하고, 검토용 HTML fragment map을 만든다. GUI는 Phase 5에서 만든다. | 초기에는 엔진의 정확도 검증이 우선이고, HTML만으로 결과 검토가 가능하다. (§7) | R-01 | Proposed |
| D-008 | Phase 1 범위는 Absolute mass vertical slice로 하고, ProSight PC 결과와 대조하는 것을 완료 기준으로 삼는다. 진행은 하위 단계마다 논의 → 결정 기록 → 구현 → 사용자 확인 순서로 한다. | 사용자의 과거 ProSight 결과가 가장 신뢰할 수 있는 검증 기준이다. 진행 방식은 사용자 지시다. (§9.1) | R-04, R-07 | **Accepted** (2026-10-08) |

## 열린 질문 (Question)

| ID | 질문 | 해소 방법 |
|---|---|---|
| Q-01 | ProSight의 2.2 Da window는 ±2.2 Da인가, 전체 폭 2.2 Da인가? | 사용자의 ProSight 결과에서 관측 질량차 분포를 확인한다. |
| Q-02 | P-score의 우연 매치 확률 p는 정확히 어떻게 파라미터화되어 있는가? | ProSight 결과의 매치 수와 P-score로 p를 역산한다. |
| Q-03 | Composition-first DB에서 E-value의 N_cand는 조성 수로 세는가, 위치 조합 수로 세는가? | Decoy 실험으로 보정 상태를 비교한다(Phase 3). |
| Q-04 | 기본 deconvolution 엔진은 TopFD와 FLASHDeconv 중 무엇으로 할 것인가? | Phase 1에서 사용자 데이터로 비교한다. |
