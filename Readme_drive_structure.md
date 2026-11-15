# PHC351 — submission directory guide

Project: **Deep Learning for Detection and Reconstruction of Gravitational-Wave Signals**  
Directory audit: 4 November 2026. The seven main folders are retained as supplied by the student.

## Start here

- **Read the work:** `Reports/PHC351_Project_Presentation.pptx` and its separate narration script.
- **Run the released models:** `Code/README_RUN_AND_EVALUATE.md`.
- **Download all source code:** `Code/PHC351_Code_and_Dependencies.zip`. This is a source/dependency/documentation bundle; datasets and trained weights have their own folders.
- **Understand the models:** `Trained Models/MODEL_CARD.md`.
- **Inspect final acoustic measurements:** `Results/Acoustic_improvement_v2/fresh_test_results.json`, `analysis.json`, and the PNG/PDF figure bundle.
- **Inspect all file locations:** `Reports/DRIVE_FILE_INVENTORY.csv`. It lists each retained file with its path, Drive ID, byte size and link.

## Folder structure

```text
Deep Learning for Detection and Reconstruction of Gravitational-Wave Signals/
├── README_DRIVE_STRUCTURE.md
├── Code/
│   ├── README_RUN_AND_EVALUATE.md
│   ├── PHC351_Code_and_Dependencies.zip
│   ├── restore_submission.py
│   ├── evaluate_submission.py
│   ├── README_GW_DATASET_REBUILD.md
│   ├── PHC351_Rebuild_v2.ipynb
│   ├── rebuild_dataset.py / validate_dataset.py / plot_dataset.py
│   ├── requirements.txt
│   ├── PHC351_Project_legacy.ipynb
│   ├── Training_pipeline_v1/
│   │   ├── PHC351_MoE_Training.ipynb
│   │   ├── training_code.zip
│   │   └── README_training_pipeline.md
│   └── Acoustic_improvement_v2/
│       ├── PHC351_Acoustic_Improvement_v2.ipynb
│       ├── improved_code_00.zip
│       ├── create_paper_figures.py
│       └── README.md
├── Datasets/
│   ├── Training data/
│   │   ├── GW_augmented_v3/          # GW train + validation ZIPs
│   │   ├── Acoustic_v1/              # corrected ESC train + validation ZIPs
│   │   ├── Acoustic_FSD_v2/          # FSD supplementary train + validation ZIPs
│   │   ├── Reconstruction_v2_train/  # original 3,000-example pilot: train NPZs
│   │   └── Reconstruction_v2_validation/
│   ├── Testing data/
│   │   ├── GW_augmented_v3/          # held-out GW test ZIP
│   │   ├── Acoustic_v1/              # historical ESC test ZIP
│   │   ├── Acoustic_FSD_v2/          # 1,000-mixture test, two ZIPs
│   │   └── Reconstruction_v2_test/   # original pilot test NPZs
│   ├── Preprocessing data/
│   │   ├── Reconstruction_v2_metadata/
│   │   ├── Training_pipeline_v1_metadata/
│   │   ├── Acoustic_improvement_v2_metadata/
│   │   └── GW150914_event_windows.npz
│   ├── Gwosc/                       # H1 source parts + source_parts.json; H1/L1 event HDF5s
│   ├── Acoustic_sources_ESC50/       # three lossless source archives
│   ├── FSD50K_sources_v2/            # eleven lossless source archives
│   └── Gravity spy/                 # empty historical placeholder; not used in this experiment
├── Trained Models/
│   ├── MODEL_CARD.md
│   ├── MoE_training_v1/              # GW and corrected acoustic checkpoint ZIPs
│   └── Acoustic_improvement_v2/      # improved_models_00.zip
├── Results/
│   ├── MoE_training_v1/              # training_results.zip, results_summary.json, reload check
│   ├── Acoustic_improvement_v2/      # metrics, predictions/results ZIPs, figures and release index
│   └── reconstruction_v2_*.png       # original dataset validation / waveform diagnostics
├── Reports/
│   ├── PHC351_Project_Presentation.pptx
│   ├── PHC351_Narration_30min.txt
│   ├── PHC351_Article_Draft.md
│   ├── SUBMISSION_STATUS.md
│   ├── DRIVE_FILE_INVENTORY.csv
│   ├── DRIVE_ORGANIZATION_AUDIT.json
│   ├── SUBMISSION_HELPERS_VERIFICATION.json
│   ├── HISTORICAL_initial_pipeline_review.md
│   ├── MoE_training_v1/training_report.md
│   └── Acoustic_improvement_v2/TRAINING_SUMMARY.md
├── Env_Setup/
│   ├── README_ENVIRONMENT.md
│   ├── requirements_training.txt
│   ├── requirements_submission.txt
│   └── reconstruction_v2_requirements.txt
└── Library Repositories/
    └── SOURCES_AND_REFERENCES.md
```

Some existing names have trailing spaces, including `Training data `, `Testing data `, `Gwosc `, `Results `, `Reports ` and `Env_Setup `. They were not renamed. The restore helper resolves these exact existing folders by comparing names with outer whitespace removed. The inventory records the actual names.

## Which data belong to each experiment?

| Release | Train / validation / test examples | Input format | Role |
|---|---:|---|---|
| Original GW reconstruction v2 | 2,100 / 450 / 450 | 4 s, 4096 Hz, `(16384,1)` | Original 3,000-example dataset and pilot history |
| GW augmented v3 | 6,000 / 600 / 600 | 4 s, 2048 Hz, `(8192,1)` | Main GW reconstruction comparison |
| Corrected acoustic v1.1 | 6,000 / 600 / 600 | 2 s, 8192 Hz, `(16384,1)` | ESC training and historical test diagnostic |
| Acoustic FSD v2 | 3,000 / 600 / 1,000 | 2 s, 8192 Hz, `(16384,1)` | Additional training/calibration and final matched acoustic benchmark |

Validation partitions are stored with training archives, but are used for model/threshold selection rather than gradient training. Never combine the held-out test partitions into training or split all augmented windows again at random.

The FSD test has 500 foreground and 500 noise-only mixtures, made from 240 held-out recordings. Source recordings are reused across mixtures; they are not 1,000 independent disaster events. The GW augmented experiment uses one H1 background recording with disjoint source-time partitions.

## Metadata and archives

Preprocessing metadata contain source identities, sample parameters, PSD/filter settings, common gains, split definitions, numerical validation and archive hashes. These are necessary for interpreting and regenerating the arrays, not disposable intermediate files.

`Training_pipeline_v1_metadata/training_release.json` and `Results/Acoustic_improvement_v2/drive_release.json` are the restoration indexes. The restore helper verifies SHA-256 before extracting. **Do not rename release archives:** for example `audio_train_000.zip` is part of the corrected ESC release despite lacking `v1_1` in its name; later corrected packs also use mixed historical names recorded in the index.

GW and ESC data ZIPs store filenames relative to their dataset directory. FSD ZIPs store full project-relative paths such as `data/acoustic_v2_fsd/...`. Model ZIPs and result ZIPs also have distinct path conventions. Use the helper instead of extracting every ZIP into one folder.

Standalone result JSONs and PNGs are deliberate browseable views of payloads also provided in release ZIPs. The ZIPs support notebook restoration; the loose views support marking/review. The combined code ZIP and versioned code ZIPs likewise serve different entry points. Raw sources, licences, the original pilot and validation records are retained for reproducibility.

## Cleanup and submission status

36 exact submission-copy archives were removed after matching their recorded copy-source IDs, current sizes and source release SHA-256 records. Canonical dataset and metadata archives remain in their original folders. The removed copies total 1,442,502,023 bytes (about 1.44 GB decimal). Unique datasets, checkpoints, source audio, scientific results and the seven main folders were preserved. The audit JSON records every removed ID and its retained equivalent.

The initial pipeline review is explicitly historical; the later training summary and frozen metrics describe the completed experiments. Gravity Spy is not used for training, and no result should be attributed to it.

See `Reports/SUBMISSION_STATUS.md` for remaining personal/submission deliverables. The project supports controlled GW reconstruction and acoustic sound-category experiments. It has not established trapped-person confirmation, localisation or field rescue readiness.
