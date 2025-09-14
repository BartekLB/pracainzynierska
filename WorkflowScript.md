graph TD
  %% Definicje stylów
  classDef input fill:#E3F2FD,stroke:#1565C0,stroke-width:1.5px,color:#000;
  classDef preprocess fill:#FFF9C4,stroke:#FBC02D,stroke-width:1.5px,color:#000;
  classDef training fill:#E8F5E9,stroke:#2E7D32,stroke-width:1.5px,color:#000;
  classDef infra fill:#F3E5F5,stroke:#6A1B9A,stroke-width:1.5px,color:#000;
  classDef output fill:#FFEBEE,stroke:#C62828,stroke-width:1.5px,color:#000;

  %% ROW 1
  D["Dane wejściowe<br>(BraTS: NIfTI, 4 modalności)"]:::input
  P["Preprocessing<br>(reorient, resample, skull-strip,<br>normalizacja z-score)"]:::preprocess
  L["Patching / Dataloader<br>(3D patch 128^3–160^3, batch,<br>augmentacje)"]:::preprocess

  %% ROW 2
  T1["Trening: nnU-Net<br>(baseline + 5-fold CV)"]:::training
  T2["Trening: Hybryda / Ensembling<br>(Optimized U-Net, Swin-UNETR,<br>łączone predykcje)"]:::training
  T3["Adaptacja: SAM / MedSAM<br>(prompting, fine-tuning/LoRA,<br>przewidywanie masek)"]:::training

  %% ROW 3
  V["Walidacja & Monitoring<br>(Dice, HD95, TTA, early-stop)"]:::infra
  R["Rejestr modeli<br>(wersjonowanie, metadane,<br>artefakty, best ckpt)"]:::infra
  PK["Pakowanie modelu<br>(ONNX/TorchScript, sygnatury I/O)"]:::infra

  %% ROW 4
  S["Serwis inferencyjny<br>(REST/gRPC, GPU 8–12 GB,<br>kolejka zadań)"]:::infra
  PP["Postprocessing<br>(agregacja patchy, czyszczenie<br>małych komponentów, smoothing)"]:::infra
  E["Ewaluacja kliniczna<br>(raporty PDF/JSON, krzywe,<br>audyt)"]:::infra

  %% ROW 5
  UI["Aplikacja WWW<br>(przeglądarka 3D, overlay masek,<br>export DICOM-SEG)"]:::output
  MLOps["MLOps<br>(CI/CD, retraining, kontrola wersji danych,<br>metryki w czasie)"]:::output
  EXT["Rozszerzalność<br>(pluginy na nowe modele, konfiguracje per task,<br>A/B testy, ensemble)"]:::output

  %% Połączenia
  D --> P --> L
  L -.-> T1
  L -.-> T2
  L -.-> T3
  T1 --> V
  T2 --> R
  T3 --> PK
  V --> R --> PK
  PK --> S --> PP --> E
  S --> UI
  PP --> MLOps
  E --> EXT
