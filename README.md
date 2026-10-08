# TDP-Assist

Top-down proteomics database search 엔진이다.
ProSight PC의 Absolute mass / Biomarker search 워크플로를 계승하고, 그 위에 현대적 개량(target-decoy FDR, 통합 proteoform 가설 모델, chimeric precursor 처리 등)을 단계적으로 얹는 것을 목표로 한다.

## 현재 상태

**Phase 1 (Absolute mass vertical slice)** 진행 중이다. 하위 단계마다 논의 → 결정 → 구현 순서로 진행하며, 기록은 [docs/phase1/PHASE1_LOG.md](docs/phase1/PHASE1_LOG.md)에 있다. 아직 코드는 없다.

## 문서

- [docs/design/DESIGN_v0.1.md](docs/design/DESIGN_v0.1.md): 전체 설계 문서다.
- [docs/DECISIONS.md](docs/DECISIONS.md): 설계 결정 로그(ADR)와 열린 질문 목록이다.
- [docs/GLOSSARY.md](docs/GLOSSARY.md): 용어와 약어 모음이다.
- [docs/phase1/PHASE1_LOG.md](docs/phase1/PHASE1_LOG.md): Phase 1의 결정 순서와 단계별 논의 기록이다.

## 처리 흐름 (계획)

```
Vendor 파일 (.raw / .d / .wiff)
  → mzML (msconvert / ThermoRawFileParser)
  → deconvolution (TopFD / FLASHDeconv → msalign)
  → 검색 (Absolute mass → Biomarker → Absolute + Δm)
  → FDR
  → TSV / SQLite / HTML report
```
