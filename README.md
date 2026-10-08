# CamoInsect

**A benchmark for open-vocabulary camouflaged insect detection.**

CamoInsect is a curated collection of insects and related arthropods that blend into natural backgrounds. It contains **3,412 images, 3,644 bounding boxes, and 21 coarse categories**, selected from IP102 and CottonInsect. The benchmark provides a fixed **15 Base / 6 Novel** open-vocabulary protocol, a fully supervised closed-set protocol, and an ordinary/camouflaged matched-domain comparison.

CamoInsect accompanies **SQ-OVCOD: Contextual Semantic Fusion and Visual Quality Calibration for Open-Vocabulary Camouflaged Object Detection** (manuscript, 2026).

## Download

**CamoInsect v1 archive (approximately 1.39 GB):** [Download from Google Drive](https://drive.google.com/file/d/1Gg0bmtXpviqlRzA5FCNN05jXFdgd2v_A/view?usp=sharing)

Download the archive and extract the `CamoInsect_v1/` directory. The archive includes the main camouflaged collection and **273 additional ordinary images** used in the matched-domain protocol. The 273 matched camouflaged images are a subset of the main collection and share its image files. There are **3,685 distinct image files** in total.

Use the download button on the Google Drive page to save `CamoInsect_v1.zip`. The archive checksum is provided in [CamoInsect_v1.zip.sha256](CamoInsect_v1.zip.sha256).

## Dataset at a glance

| Collection or protocol | Categories | Training images | Training boxes | Test images | Test boxes |
|---|---:|---:|---:|---:|---:|
| CamoInsect, closed set | 21 | 2,388 | 2,562 | 1,024 | 1,082 |
| CamoInsect, open vocabulary | 15 Base / 6 Novel | 1,974 | 2,074 | 1,024 | 1,082 |
| Matched camouflaged domain | 14 active | 187 | 201 | 86 | 94 |
| Matched ordinary domain | 14 active | 187 | 198 | 86 | 99 |

The main collection contains **2,089 IP102 images with 2,294 boxes** and **1,323 CottonInsect images with 1,350 boxes**. The categories are operational coarse groups at mixed taxonomic ranks; they include pests, natural enemies such as lacewings, and one mite group, Acari.

## Evaluation protocols

### Open-vocabulary detection

- Train using the supplied `annotations/open_vocabulary/train_base.json` and its corresponding image list.
- The Base/Novel category partition is fixed in `protocols/open_vocabulary/category_split.json`.
- **All 414 closed-set training images containing Novel objects are withheld as whole images**, leaving 1,974 training images and 2,074 Base boxes. The withheld list is supplied in `excluded_train.json`.
- Evaluate on the unchanged 1,024-image test set with **all 21 categories competing together**. Novel categories have 216 test boxes in 177 test images.
- Use the fixed model-facing English vocabulary in `protocols/open_vocabulary/vocabulary.json`. These are category names or short category-group descriptions, not image-specific referring sentences. The Latin category names remain the stable taxonomic identifiers.
- Novel categories are absent from this protocol's task-specific supervised training. This designation does not assert that they were absent from a model's public pretraining.

Report COCO bounding-box AP over IoU thresholds 0.50–0.95, with All, Base, and Novel results. Retain the original category IDs when exporting predictions. The accompanying paper evaluates with COCO `maxDets = [1, 10, 100]`.

### Closed-set detection

Use `annotations/closed_set/train.json` for training and `annotations/closed_set/test.json` for testing, with all 21 categories supervised. `all.json` combines the two fixed splits for dataset inspection; it is not a training split.

The release supplies COCO, VOC, and YOLO annotations for the main collection. There is **no separate validation split**. The provided configuration helper leaves `val` unset rather than treating the test set as validation.

### Matched ordinary/camouflaged comparison

Each domain contains 187 training images and 86 test images from 14 shared categories. Image counts are matched within source, category, and split groups. The selection also accounts for relative object area and instance count. Box counts differ because individual images can contain different numbers of objects.

Train each detector on each domain using the same initialization and training budget, and evaluate both models on both test domains. This produces four combinations: ordinary→ordinary, ordinary→camouflaged, camouflaged→ordinary, and camouflaged→camouflaged.

The active category IDs are `1, 2, 4, 5, 6, 10, 11, 12, 14, 15, 16, 17, 19, 21`. Matched COCO files retain the full 21-category metadata for a consistent category mapping; only these 14 categories have annotations. The archive includes the **273-image matched ordinary subset**, not the larger 502-image ordinary control pool described during benchmark construction.

## Categories and vocabulary

| COCO ID | Latin category | Model-facing English text | OVD group |
|---:|---|---|---|
| 1 | Lepidoptera | moths, butterflies and caterpillars | Base |
| 2 | Miridae | plant bugs | Base |
| 3 | Pentatomidae | stink bugs | Novel |
| 4 | Cicadellidae | leafhoppers | Base |
| 5 | Fulgoroidea | planthoppers | Novel |
| 6 | Aphididae | aphids | Novel |
| 7 | Aleyrodidae | whiteflies | Base |
| 8 | Coccomorpha | scale insects and mealybugs | Base |
| 9 | Chrysopidae | green lacewings | Base |
| 10 | Orthoptera | grasshoppers, locusts, crickets and katydids | Novel |
| 11 | Coccinellidae | ladybird beetles | Base |
| 12 | Curculionoidea | weevils | Novel |
| 13 | Cerambycidae | longhorn beetles | Base |
| 14 | Scarabaeidae | scarab beetles | Base |
| 15 | Elateridae | click beetles and wireworms | Base |
| 16 | Meloidae | blister beetles | Novel |
| 17 | Chrysomelidae | leaf beetles | Base |
| 18 | Diptera | flies | Base |
| 19 | Hymenoptera | wasps, bees, ants and sawflies | Base |
| 20 | Thysanoptera | thrips | Base |
| 21 | Acari | mites | Base |

The same mapping is available in `categories.csv`.

## Archive structure

```text
CamoInsect_v1/
├── README.md
├── THIRD_PARTY_NOTICES.md
├── REFERENCES.bib
├── categories.csv
├── images/                         # 3,412 main-collection images
├── matched/
│   └── ordinary/images/             # 273 additional ordinary images
├── annotations/
│   ├── closed_set/
│   │   ├── all.json
│   │   ├── train.json
│   │   └── test.json
│   ├── open_vocabulary/
│   │   ├── train_base.json
│   │   └── test.json
│   ├── matched/
│   │   ├── ordinary_train.json
│   │   ├── ordinary_test.json
│   │   ├── camouflage_train.json
│   │   └── camouflage_test.json
│   └── voc/                        # Main-collection VOC XML files
├── labels/                         # Main-collection YOLO labels
├── protocols/
│   ├── closed_set/                 # train.txt, test.txt
│   ├── open_vocabulary/
│   │   ├── train_base.txt
│   │   ├── test.txt
│   │   ├── category_split.json
│   │   ├── excluded_train.json
│   │   └── vocabulary.json
│   └── matched/                    # One image list per domain/split
├── metadata/
│   ├── image_manifest.csv
│   ├── source_annotations.csv
│   └── statistics.json
└── tools/
    ├── verify_dataset.py
    └── make_yolo_config.py
```

### Annotation and path conventions

- COCO `file_name` paths are relative to the extracted `CamoInsect_v1/` root. Set your data loader's image root to this directory. COCO boxes use zero-based `[x, y, width, height]` in image pixels. Categories use the original **1-based IDs 1–21**.
- VOC boxes use 1-based inclusive `[xmin, ymin, xmax, ymax]` coordinates.
- YOLO rows use `class_index x_center y_center width height`, with normalized coordinates and **0-based global class indices 0–20**. For these supplied labels, `class_index = COCO category_id - 1`.
- OVD Base training retains the original, nonconsecutive COCO IDs. A model that needs 15 consecutive training labels must map them in this order: `[1, 2, 4, 7, 8, 9, 11, 13, 14, 15, 17, 18, 19, 20, 21]` → `[0, ..., 14]`. Map predictions back to the original COCO IDs before evaluation. Do not confuse this compact mapping with the global YOLO mapping.
- Image lists contain paths relative to the extracted dataset root, such as `images/COTTON_00000.jpg` or `matched/ordinary/images/COTTON_01485.jpg`.
- COCO image and annotation IDs are local to the relevant annotation file. Use `source_id` or the image filename to associate images across protocols; do not join different protocols using numerical image IDs alone.
- `image_manifest.csv` records image provenance, source-relative names, and file hashes. `source_annotations.csv` preserves source labels and boxes. Historical fine labels describe source provenance; the released detection targets are the 21 coarse categories.

## Verify and use

From the extracted dataset root, run:

```bash
python tools/verify_dataset.py
```

The verifier checks image hashes, expected counts, COCO references, and the whole-image exclusion used by the OVD protocol. It uses the Python standard library and does not require a GPU.

To create a portable local configuration for **closed-set YOLO training**:

```bash
python tools/make_yolo_config.py --output camoinsect_closed_set.yaml
```

The helper resolves image paths for the local extraction directory and does not assign the test set as validation. For OVD and matched-domain experiments, use their dedicated COCO files and image lists.

## Source datasets and terms of use

CamoInsect is curated from existing **IP102** and **CottonInsect** images. Original photographs belong to their respective rights holders. Dataset construction contributes camouflage-focused selection, category reconciliation, annotation preparation, fixed splits, and evaluation protocols.

- **IP102:** [official repository](https://github.com/xpwu95/IP102). The source README permits academic use; other uses should follow its stated contact procedure.
- **CottonInsect:** [dataset record and DOI](https://doi.org/10.57760/sciencedb.j00001.00866). The dataset record specifies [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).

See [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) for source attribution and license details. Original source-image terms continue to apply.

## Citation

If you use CamoInsect, acknowledge this release and the accompanying manuscript:

> SQ-OVCOD: Contextual Semantic Fusion and Visual Quality Calibration for Open-Vocabulary Camouflaged Object Detection. Manuscript, 2026.

Please also cite **IP102**, the **CottonInsect data paper**, and the **CottonInsect dataset record**. Their bibliographic entries are provided in [REFERENCES.bib](REFERENCES.bib). The manuscript citation will be updated with its final bibliographic information when available.
