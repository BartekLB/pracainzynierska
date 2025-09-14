flowchart TD
  %% === STYLE ===
  classDef app fill:#E3F2FD,stroke:#1565C0,stroke-width:1.4px,color:#000;
  classDef data fill:#E0F7FA,stroke:#006064,stroke-width:1.4px,color:#000;
  classDef curate fill:#EDE7F6,stroke:#4527A0,stroke-width:1.4px,color:#000;
  classDef train fill:#E8F5E9,stroke:#2E7D32,stroke-width:1.4px,color:#000;
  classDef registry fill:#F5F5F5,stroke:#424242,stroke-width:1.4px,color:#000;
  classDef deploy fill:#FFF9C4,stroke:#F9A825,stroke-width:1.4px,color:#000;
  classDef monitor fill:#FFEBEE,stroke:#C62828,stroke-width:1.4px,color:#000;
  linkStyle default stroke:#666,stroke-width:1.1px;

  %% === APPLICATION (data generation & feedback) ===
  A1["Aplikacja WWW<br/>integracja z modelem (inferencja)"]:::app
  A2["Zapis predykcji + metadane<br/>(DICOM-SEG/JSON, wersjonowanie)"]:::app
  A3["Korekta maski przez lekarza<br/>(edytor anotacji)"]:::app
  A4["Ręczne zaznaczenie obszaru (opcjonalnie)<br/>dla nowych przypadków / prompt do SAM"]:::app
  A5["Poprawki w zbieraniu zdjęć<br/>(checklista jakości, walidacja wejścia)"]:::app
  A6["Udostępnianie badań + maski<br/>(portal upload do dalszych badań)"]:::app

  %% === DATA LAYER ===
  D1["Magazyn danych + etykiet<br/>(DVC / Git-LFS / S3)"]:::data
  Q1["Anonimizacja, standaryzacja, QC<br/>(DICOM→NIfTI, reorient, resample)"]:::curate
  RDATA["Rejestr datasetów<br/>(MLflow/W&B Artifacts)"]:::registry

  %% === TRAINING / REGISTRY ===
  T1["Trening modeli<br/>(nnU-Net / Hybryda / SAM)"]:::train
  T2["Ewaluacja + raporty jakości<br/>(Dice, HD95, krzywe)"]:::train
  MREG["Rejestr modeli<br/>(MLflow Model Registry)"]:::registry
  CI["CI/CD modeli i danych<br/>(GitHub Actions + DVC)"]:::registry

  %% === DEPLOYMENT ===
  PKG["Pakowanie modelu<br/>(ONNX/TorchScript, Docker)"]:::deploy
  DEP["Deployment serwisu<br/>(NVIDIA Triton + FastAPI/gRPC)"]:::deploy

  %% === MONITORING & RETRAIN ===
  MON["Monitoring wydajności i jakości<br/>(Prometheus/Grafana, drift check)"]:::monitor
  RT["Trigger retraining<br/>(nowe dane, spadek jakości)"]:::monitor

  %% === FLOWS: App → Data ===
  A1 --> A2
  A2 --> D1
  A3 --> D1
  A4 --> D1
  A6 --> D1
  A5 -. notyfikacje .-> Q1

  %% === Data curation ===
  D1 --> Q1 --> RDATA

  %% === Training → Registry → Deploy ===
  RDATA --> T1 --> T2
  T1 --> MREG
  MREG --> CI --> PKG --> DEP

  %% === Serving back to App ===
  DEP --> A1

  %% === Monitoring loop ===
  DEP --> MON
  MON --> RT --> T1

  %% === LEGEND ===
  subgraph Legend [Legenda]
    direction LR
    L1["Aplikacja / dane z kliniki"]:::app
    L2["Dane & kuracja (QC, standaryzacja)"]:::data
    L3["Trening / Ewaluacja"]:::train
    L4["Rejestry / CI-CD"]:::registry
    L5["Wdrożenie (serving)"]:::deploy
    L6["Monitoring & Retraining"]:::monitor
  end
