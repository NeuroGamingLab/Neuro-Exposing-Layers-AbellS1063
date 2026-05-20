# Galaxy Cluster Abell S1063 Layers

Unsupervised palette compression on **potm2505a** (galaxy cluster Abell S1063): **MiniBatchKMeans** (mini-batch k-means, scikit-learn–compatible API) partitions a large set of RGB pixel samples into *k* clusters and exposes each cluster centroid as a discrete swatch, trading high-cardinality colour sets for a low-cardinality representative palette.

The repository is organized as **one folder per subject** (`images/`). Each file is a still from a single **potm2505a** session.

**The application** treats each still as a field of color samples. **MiniBatchKMeans**—a lightweight **k-means** variant built for large batches—clusters tens of thousands of extracted colors into a **small set of representative swatches**. That cuts storage and downstream work, makes palettes easier to compare across layers or sessions, and summarizes palette structure without tuning every hue by hand. The approach is vector clustering in color space, chosen for speed when the raw sample count is large. The stills below the original are **AI-generated layer outputs** from that pipeline: each variant applies the same explanation of the source frame to a different palette partition or layer reconstruction.

| Item | Count |
| --- | --- |
| Source (`potm2505a-ORIGINAL.tif`) | 1 |
| Derived stills (timestamp-prefixed `*-potm2505a.tif`) | 22 |
| Gallery layout | 2 × 11 |
| README previews (`images/png/`) | PNG exports of each TIFF |

Source stills are **TIFF** in `images/`; **PNG** copies in `images/png/` (downscaled to 1600 px on the long edge) are used below so GitHub can render the gallery inline.

## Original — `Galaxy Cluster Abell S1063`

![potm2505a-ORIGINAL.tif](images/png/potm2505a-ORIGINAL.png)

_Source: [https://esawebb.org/images/potm2505a/](https://esawebb.org/images/potm2505a/)_

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

## Related work

- [NeuroGamingLab/Neuro-Exposing-Layers-QuestionMarkGalaxy](https://github.com/NeuroGamingLab/Neuro-Exposing-Layers-QuestionMarkGalaxy) — same layer-exposure layout on the Question Mark Galaxy (**questionmark1**).

---

*Designed and Engineered by Dang Hoang. AI Engineer.*
