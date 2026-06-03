# Galaxy Cluster Abell S1063 Layers

Unsupervised palette compression on **potm2505a** (galaxy cluster Abell S1063): **MiniBatchKMeans** (mini-batch k-means, scikit-learn–compatible API) partitions a large set of RGB pixel samples into *k* clusters and exposes each cluster centroid as a discrete swatch, trading high-cardinality colour sets for a low-cardinality representative palette.

The repository is organized as **one folder per subject** (`images/`). Each file is a still from a single **potm2505a** session.

**The application** treats each still as a field of color samples. **MiniBatchKMeans**—a lightweight **k-means** variant built for large batches—clusters tens of thousands of extracted colors into a **small set of representative swatches**. That cuts storage and downstream work, makes palettes easier to compare across layers or sessions, and summarizes palette structure without tuning every hue by hand. The approach is vector clustering in color space, chosen for speed when the raw sample count is large. The stills below the original are **AI-generated layer outputs** from that pipeline: each variant applies the same explanation of the source frame to a different palette partition or layer reconstruction.


| Item                                                          | Count                             |
| ------------------------------------------------------------- | --------------------------------- |
| Source (`potm2505a-ORIGINAL.tif`)                             | 1                                 |
| Derived stills (timestamp-prefixed `*-potm2505a.tif`)         | 22                                |
| Gallery layout                                                | 2 × 11                            |
| README previews (`images/png/`)                               | PNG exports of each TIFF          |
| Enhanced stills (`images/potm2505a-enhanced/*_potm2505a.tif`) | 23                                |
| Merged palette (`*_merged_palette.tif`)                       | 1                                 |
| Enhanced collage (`potm2505a_collage_4x6.png`)                | 1                                 |
| Enhanced gallery layout                                       | 2 × 12                            |
| Enhanced README previews (`images/potm2505a-enhanced/png/`)   | PNG exports of each enhanced TIFF |


Source stills are **TIFF** in `images/`; **PNG** copies in `images/png/` (downscaled to 1600 px on the long edge) are used below so GitHub can render the gallery inline. Enhanced outputs live in `images/potm2505a-enhanced/` with matching PNG previews in `images/potm2505a-enhanced/png/`.

## Pipeline with matrix reduction

Conceptual flow :

```text
pixels-org/*.png  →  colorgram (dominant colors, optional 255−RGB)
        ↓
optional matrix reduction  (--reduction, --reduction-target)
  • PCA / NMF / ICA  → mainly on palette rows (compress / denoise swatches)
  • Robust PCA (rpca) → palette outlier drop or image low-rank luminance smooth
        ↓
target image(s) in images/  (--image-glob, optional --max-side)
        ↓
nearest RGB snap (cKDTree)  →  images/saved/
```

**Outputs**


| Mode          | Files                                                                                        |
| ------------- | -------------------------------------------------------------------------------------------- |
| `per-palette` | `{timestamp}_{source_stem}.{ext}` — one file per painting palette (no `_via_<name>` suffix). |
| `merged`      | `{timestamp}_{source_stem}_merged_palette.{ext}` — all palette colors combined.              |


Default target image: `**potm2505a.tif`** (6920×6615 RGB TIFF under `images/`). Use `**--max-side 2048**` (or similar) for faster trials. Enhanced stills in this repo are published under `images/potm2505a-enhanced/`.

### Run

From the pipeline repo root (or pass `--root /path/to/pythonProject89Plus-2`):

```bash
python MatrixNNEngine9.py --help
```

**Default** — per-palette + merged on `potm2505a.tif`:

```bash
python MatrixNNEngine9.py
```

**Recommended first run** on large TIFFs:

```bash
python MatrixNNEngine9.py --max-side 2048
```

**Merged only** (single output):

```bash
python MatrixNNEngine9.py --mode merged --max-side 2048
```

**Other targets** (PNG, GOES SUVI, etc.):

```bash
python MatrixNNEngine9.py --image-glob "GOES-16SUVI-2024-10-11-02-04-36.png"
python MatrixNNEngine9.py --image-glob "*.png" --mode merged --max-side 4096
```

**Stricter snapping** — keep original pixel if nearest palette color is farther than 45 in RGB L2:

```bash
python MatrixNNEngine9.py --max-rgb-distance 45 --fallback original
```

**Jupyter preview** (requires `ipython`):

```bash
python MatrixNNEngine9.py --display
```

### Matrix reduction 

Background and motivation are in `**enhancement-instruction.txt**`. In this project, reduction is **feature engineering before palette snap**, not RNN/LSTM/RL (those are noted as possible future consumers of the same preprocessing idea).


| `--reduction` | `--reduction-target palette`                                       | `--reduction-target image`                       |
| ------------- | ------------------------------------------------------------------ | ------------------------------------------------ |
| `pca`         | Cluster palette colors in PC space → fewer representative swatches | Mild RGB denoise via subsampled PCA              |
| `nmf`         | Non-negative basis rows → parts-based palette                      | Light correction from subsampled NMF             |
| `ica`         | Independent directions → RGB palette samples                       | Light correction from subsampled ICA             |
| `rpca`        | Drop outlier palette swatches (sparse entries)                     | Low-rank luminance (truncated SVD), then recolor |
| `none`        | No reduction (default)                                             | No reduction                                     |


Shared flags: `**--reduction-components`** (default `64`), `**--reduction-target**` `palette`  `image`  `both`.

**Examples**

```bash
# Compress merged palette before snap (large colorgram lists)
python MatrixNNEngine9.py --mode merged --max-side 2048 \
  --reduction pca --reduction-target palette --reduction-components 64

# Smooth image structure, then snap (Robust PCA / low-rank luma)
python MatrixNNEngine9.py --reduction rpca --reduction-target image \
  --reduction-components 12 --max-side 2048

# Both palette and image preprocessing
python MatrixNNEngine9.py --reduction pca --reduction-target both --reduction-components 32
```

### `MatrixNNEngine9.py` flags


| Flag                              | Meaning                                                             |
| --------------------------------- | ------------------------------------------------------------------- |
| `--root`                          | Project root; palette/image paths are relative to this.             |
| `--palette-dir`, `--palette-glob` | Palette paintings (default `pixels-org`, `*.png`).                  |
| `--image-dir`, `--image-glob`     | Target images (default `images/`, `potm2505a.tif`).                 |
| `--output-dir`                    | Output folder (default `images/saved`).                             |
| `--colors`                        | Dominant colors per palette image via `colorgram` (default `1000`). |
| `--no-reverse`                    | Skip `255 − rgb` after extraction.                                  |
| `--mode`                          | `both` | `per-palette` | `merged`.                                  |
| `--tile-height`                   | Strip height for streaming large images (default `512`).            |
| `--max-rgb-distance`              | Optional L2 RGB gate; use with `--fallback`.                        |
| `--fallback`                      | `original` | `white` | `carry` when gate rejects a snap.            |
| `--max-side`                      | Downscale if longest side exceeds N pixels.                         |
| `--sleep`                         | Seconds between writes (default `0`).                               |
| `--reduction`                     | `none` | `pca` | `nmf` | `ica` | `rpca`.                            |
| `--reduction-target`              | `palette` | `image` | `both`.                                       |
| `--reduction-components`          | Components / rank / swatch count (default `64`).                    |
| `--display`                       | Inline display in IPython.                                          |


See `**palette-pipeline-checklist.md**` for a printable run/verify checklist.

### Notes (palette pipeline)

- Matching is **nearest-neighbor in RGB Euclidean space** (`cKDTree`), not legacy `difflib` string matching on RGB tuples.
- `**enhancement-instruction.txt`** also discusses RNN/LSTM/RL; those are **not** wired into `MatrixNNEngine9.py` today—only the reduction preprocessors are.
- With `--max-rgb-distance`, `**carry`** uses the previous output pixel in the same **column**, scanning **top to bottom** within each vertical strip (`x` outer, `y` inner).
- Legacy notebook-style scripts (`MatrixNNEngine9v2.py`, old monolithic loops) remain for reference; prefer `**MatrixNNEngine9.py`** for new runs.

## Original — `Galaxy Cluster Abell S1063`

![potm2505a-ORIGINAL.tif](images/png/potm2505a-ORIGINAL.png)

*Source: [https://esawebb.org/images/potm2505a/](https://esawebb.org/images/potm2505a/)*

**Galaxy cluster Abell S1063** (*A glimpse of the distant past*, 6920×6615, TIFF) is the reference still for this session—a deep-field **James Webb Space Telescope** NIRCam view of the cluster, where gravitational lensing magnifies distant background galaxies. 

## Derived stills (2 × 11)

Each tile uses the explanation above: a **MiniBatchKMeans** palette summary of **potm2505a**, exposed as a discrete layer still. Filenames embed a session timestamp prefix; all tiles share the `potm2505a` stem.


| [![1779141471392879-potm2505a.png](images/png/1779141471392879-potm2505a.png)](images/png/1779141471392879-potm2505a.png)<br>1779141471392879-potm2505a.tif | [![17791425601949818-potm2505a.png](images/png/17791425601949818-potm2505a.png)](images/png/17791425601949818-potm2505a.png)<br>17791425601949818-potm2505a.tif |
| --- | --- |
| [![1779143980287241-potm2505a.png](images/png/1779143980287241-potm2505a.png)](images/png/1779143980287241-potm2505a.png)<br>1779143980287241-potm2505a.tif | [![1779145395357242-potm2505a.png](images/png/1779145395357242-potm2505a.png)](images/png/1779145395357242-potm2505a.png)<br>1779145395357242-potm2505a.tif |
| [![17791465738425841-potm2505a.png](images/png/17791465738425841-potm2505a.png)](images/png/17791465738425841-potm2505a.png)<br>17791465738425841-potm2505a.tif | [![1779147654524796-potm2505a.png](images/png/1779147654524796-potm2505a.png)](images/png/1779147654524796-potm2505a.png)<br>1779147654524796-potm2505a.tif |
| [![17791489012569668-potm2505a.png](images/png/17791489012569668-potm2505a.png)](images/png/17791489012569668-potm2505a.png)<br>17791489012569668-potm2505a.tif | [![1779150093764957-potm2505a.png](images/png/1779150093764957-potm2505a.png)](images/png/1779150093764957-potm2505a.png)<br>1779150093764957-potm2505a.tif |
| [![17791515236554-potm2505a.png](images/png/17791515236554-potm2505a.png)](images/png/17791515236554-potm2505a.png)<br>17791515236554-potm2505a.tif | [![1779152945503354-potm2505a.png](images/png/1779152945503354-potm2505a.png)](images/png/1779152945503354-potm2505a.png)<br>1779152945503354-potm2505a.tif |
| [![1779153727220831-potm2505a.png](images/png/1779153727220831-potm2505a.png)](images/png/1779153727220831-potm2505a.png)<br>1779153727220831-potm2505a.tif | [![17791550944409049-potm2505a.png](images/png/17791550944409049-potm2505a.png)](images/png/17791550944409049-potm2505a.png)<br>17791550944409049-potm2505a.tif |
| [![17791564212817411-potm2505a.png](images/png/17791564212817411-potm2505a.png)](images/png/17791564212817411-potm2505a.png)<br>17791564212817411-potm2505a.tif | [![17791571659367192-potm2505a.png](images/png/17791571659367192-potm2505a.png)](images/png/17791571659367192-potm2505a.png)<br>17791571659367192-potm2505a.tif |
| [![1779158503012853-potm2505a.png](images/png/1779158503012853-potm2505a.png)](images/png/1779158503012853-potm2505a.png)<br>1779158503012853-potm2505a.tif | [![1779159599631555-potm2505a.png](images/png/1779159599631555-potm2505a.png)](images/png/1779159599631555-potm2505a.png)<br>1779159599631555-potm2505a.tif |
| [![1779160601762002-potm2505a.png](images/png/1779160601762002-potm2505a.png)](images/png/1779160601762002-potm2505a.png)<br>1779160601762002-potm2505a.tif | [![1779161986873256-potm2505a.png](images/png/1779161986873256-potm2505a.png)](images/png/1779161986873256-potm2505a.png)<br>1779161986873256-potm2505a.tif |
| [![177916338562231-potm2505a.png](images/png/177916338562231-potm2505a.png)](images/png/177916338562231-potm2505a.png)<br>177916338562231-potm2505a.tif | [![17791648458670888-potm2505a.png](images/png/17791648458670888-potm2505a.png)](images/png/17791648458670888-potm2505a.png)<br>17791648458670888-potm2505a.tif |
| [![1779166269958907-potm2505a.png](images/png/1779166269958907-potm2505a.png)](images/png/1779166269958907-potm2505a.png)<br>1779166269958907-potm2505a.tif | [![1779189379621205-potm2505a.png](images/png/1779189379621205-potm2505a.png)](images/png/1779189379621205-potm2505a.png)<br>1779189379621205-potm2505a.tif |



## Enhanced layers — `potm2505a-enhanced`

A second pass on **potm2505a** via the **palette pipeline with matrix reduction** (`MatrixNNEngine9.py`): painting palettes extracted with **colorgram**, optional **PCA / NMF / ICA / Robust PCA** preprocessing, then **nearest RGB snap** on the Webb deep field. This set includes **23** per-palette layer stills, one **merged palette** composite, and a **4 × 6 collage** overview.

### Collage overview

![potm2505a_collage_4x6.png](images/potm2505a-enhanced/png/potm2505a_collage_4x6.png)

### Enhanced stills (2 × 12)


| [![1780431683166_potm2505a.png](images/potm2505a-enhanced/png/1780431683166_potm2505a.png)](images/potm2505a-enhanced/png/1780431683166_potm2505a.png)<br>1780431683166_potm2505a.tif | [![1780431690407_potm2505a.png](images/potm2505a-enhanced/png/1780431690407_potm2505a.png)](images/potm2505a-enhanced/png/1780431690407_potm2505a.png)<br>1780431690407_potm2505a.tif |
| --- | --- |
| [![1780431697780_potm2505a.png](images/potm2505a-enhanced/png/1780431697780_potm2505a.png)](images/potm2505a-enhanced/png/1780431697780_potm2505a.png)<br>1780431697780_potm2505a.tif | [![1780431704827_potm2505a.png](images/potm2505a-enhanced/png/1780431704827_potm2505a.png)](images/potm2505a-enhanced/png/1780431704827_potm2505a.png)<br>1780431704827_potm2505a.tif |
| [![1780431712174_potm2505a.png](images/potm2505a-enhanced/png/1780431712174_potm2505a.png)](images/potm2505a-enhanced/png/1780431712174_potm2505a.png)<br>1780431712174_potm2505a.tif | [![1780431719099_potm2505a.png](images/potm2505a-enhanced/png/1780431719099_potm2505a.png)](images/potm2505a-enhanced/png/1780431719099_potm2505a.png)<br>1780431719099_potm2505a.tif |
| [![1780431726328_potm2505a.png](images/potm2505a-enhanced/png/1780431726328_potm2505a.png)](images/potm2505a-enhanced/png/1780431726328_potm2505a.png)<br>1780431726328_potm2505a.tif | [![1780431733741_potm2505a.png](images/potm2505a-enhanced/png/1780431733741_potm2505a.png)](images/potm2505a-enhanced/png/1780431733741_potm2505a.png)<br>1780431733741_potm2505a.tif |
| [![1780431740568_potm2505a.png](images/potm2505a-enhanced/png/1780431740568_potm2505a.png)](images/potm2505a-enhanced/png/1780431740568_potm2505a.png)<br>1780431740568_potm2505a.tif | [![1780431747337_potm2505a.png](images/potm2505a-enhanced/png/1780431747337_potm2505a.png)](images/potm2505a-enhanced/png/1780431747337_potm2505a.png)<br>1780431747337_potm2505a.tif |
| [![1780431754480_potm2505a.png](images/potm2505a-enhanced/png/1780431754480_potm2505a.png)](images/potm2505a-enhanced/png/1780431754480_potm2505a.png)<br>1780431754480_potm2505a.tif | [![1780431760989_potm2505a.png](images/potm2505a-enhanced/png/1780431760989_potm2505a.png)](images/potm2505a-enhanced/png/1780431760989_potm2505a.png)<br>1780431760989_potm2505a.tif |
| [![1780431767292_potm2505a.png](images/potm2505a-enhanced/png/1780431767292_potm2505a.png)](images/potm2505a-enhanced/png/1780431767292_potm2505a.png)<br>1780431767292_potm2505a.tif | [![1780431774578_potm2505a.png](images/potm2505a-enhanced/png/1780431774578_potm2505a.png)](images/potm2505a-enhanced/png/1780431774578_potm2505a.png)<br>1780431774578_potm2505a.tif |
| [![1780431781525_potm2505a.png](images/potm2505a-enhanced/png/1780431781525_potm2505a.png)](images/potm2505a-enhanced/png/1780431781525_potm2505a.png)<br>1780431781525_potm2505a.tif | [![1780431788557_potm2505a.png](images/potm2505a-enhanced/png/1780431788557_potm2505a.png)](images/potm2505a-enhanced/png/1780431788557_potm2505a.png)<br>1780431788557_potm2505a.tif |
| [![1780431795901_potm2505a.png](images/potm2505a-enhanced/png/1780431795901_potm2505a.png)](images/potm2505a-enhanced/png/1780431795901_potm2505a.png)<br>1780431795901_potm2505a.tif | [![1780431803023_potm2505a.png](images/potm2505a-enhanced/png/1780431803023_potm2505a.png)](images/potm2505a-enhanced/png/1780431803023_potm2505a.png)<br>1780431803023_potm2505a.tif |
| [![1780431810030_potm2505a.png](images/potm2505a-enhanced/png/1780431810030_potm2505a.png)](images/potm2505a-enhanced/png/1780431810030_potm2505a.png)<br>1780431810030_potm2505a.tif | [![1780431817294_potm2505a.png](images/potm2505a-enhanced/png/1780431817294_potm2505a.png)](images/potm2505a-enhanced/png/1780431817294_potm2505a.png)<br>1780431817294_potm2505a.tif |
| [![1780431824575_potm2505a.png](images/potm2505a-enhanced/png/1780431824575_potm2505a.png)](images/potm2505a-enhanced/png/1780431824575_potm2505a.png)<br>1780431824575_potm2505a.tif | [![1780431831389_potm2505a.png](images/potm2505a-enhanced/png/1780431831389_potm2505a.png)](images/potm2505a-enhanced/png/1780431831389_potm2505a.png)<br>1780431831389_potm2505a.tif |
| [![1780431838628_potm2505a.png](images/potm2505a-enhanced/png/1780431838628_potm2505a.png)](images/potm2505a-enhanced/png/1780431838628_potm2505a.png)<br>1780431838628_potm2505a.tif | [![1780431845660_potm2505a_merged_palette.png](images/potm2505a-enhanced/png/1780431845660_potm2505a_merged_palette.png)](images/potm2505a-enhanced/png/1780431845660_potm2505a_merged_palette.png)<br>1780431845660_potm2505a_merged_palette.tif |



## Related work

- [NeuroGamingLab/Neuro-Exposing-Layers-QuestionMarkGalaxy](https://github.com/NeuroGamingLab/Neuro-Exposing-Layers-QuestionMarkGalaxy) — same layer-exposure layout on the Question Mark Galaxy (**questionmark1**).

---

*Designed and Engineered by Dang Hoang. AI Engineer.*
