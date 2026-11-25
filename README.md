<h1 align="center">apply ants transform</h1>

<p align="center">
  Applies an existing ANTs or FSL transform to diffusion metric maps (tensor FA/MD/RD/AD or NODDI NDI/ISOVF/ODI) with ANTs antsApplyTransforms, resampling them onto a T1w or T2w reference.
</p>

<p align="center">
  <a href="https://github.com/brainlife/abcd-spec"><img src="https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg" alt="ABCD Spec v1.1"></a>
  <a href="https://doi.org/10.25663/brainlife.app.979"><img src="https://img.shields.io/badge/Brainlife-brainlife.app.979-blue.svg" alt="Run on brainlife.io"></a>
</p>

## At a glance

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Applies an existing ANTs or FSL transform to diffusion metric maps (tensor FA/MD/RD/AD or NODDI NDI/ISOVF/ODI) with ANTs antsApplyTransforms, resampling them onto a T1w or T2w reference.**

Wraps antsApplyTransforms to move scalar diffusion maps into another space using a precomputed transform (warp, inverse warp and affine). Provide tensor and/or NODDI maps; each present scalar map is resliced. The reference grid is the T1w image if given, otherwise the T2w image; one of them is required. The NODDI direction field is deliberately not transformed because reslicing vector components would not rotate them correctly. It does not estimate a registration.

| | |
|---|---|
| **Inputs** | `neuro/transform/nifti` · `neuro/tensor` (optional) · `neuro/noddi` (optional) · `neuro/anat/t1w` (optional) · `neuro/anat/t2w` (optional) |
| **Outputs** | `neuro/tensor` |
| **Container** | `brainlife/ants:2.2.0-1bc` |
| **Resources** | 1 CPUs · 0.17 h walltime |
| **Run it** | [doi.org/10.25663/brainlife.app.979](https://doi.org/10.25663/brainlife.app.979) |

#### Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `transform` | `neuro/transform/nifti` | yes | Precomputed transform: nonlinear warp, inverse warp and affine matrix (ANTs format, or FSL FLIRT/FNIRT when fsl_transform is set). The warp matching the chosen direction must be present unless affine_only is set. |
| `moving_tensor` | `neuro/tensor` | no | Optional tensor scalar maps (FA, MD, RD, AD) to transform. |
| `moving_noddi` | `neuro/noddi` | no | Optional NODDI scalar maps (NDI, ISOVF, ODI) to transform. |
| `fixed_T1` | `neuro/anat/t1w` | no | Optional T1w image used as the reference grid (first choice). |
| `fixed_t2` | `neuro/anat/t2w` | no | Optional T2w image used as the reference grid if no T1w reference is given. |

#### Outputs

| Output | Datatype | Description |
|---|---|---|
| `transformed` | `neuro/tensor` | The diffusion metric map(s), resliced onto the reference grid. |

<details>
<summary><b>Parameters</b> (4)</summary>

| Parameter | Type | Default | Description |
|---|---|---|---|
| `interpolation` | enum | `Linear` | Interpolation method for reslicing. Options: `Linear`, `NearestNeighbor`, `BSpline`, `MultiLabel`. |
| `inverse` | boolean | `false` | Apply inverse transformations |
| `affine_only` | boolean | `false` | Apply only the affine transformation, skip any warp field |
| `fsl_transform` | boolean | `false` | If true, converts affine and warp from FSL to ANTs-compatible format |

</details>

<!-- brainlife-app:end -->

## Versions on brainlife.io

This repository hosts several Apps, each built from its own branch.

| App | Branch | Run it | Status |
|---|---|---|---|
| Apply Transformations to Diffusion Metrics | [`diffmetrics`](https://github.com/gamorosino/app-apply-ants-transform/tree/diffmetrics) | [doi:10.25663/brainlife.app.979](https://doi.org/10.25663/brainlife.app.979) | current |
| Apply transformations to mask | [`main`](https://github.com/gamorosino/app-apply-ants-transform/tree/main) | [doi:10.25663/brainlife.app.893](https://doi.org/10.25663/brainlife.app.893) | available |
| Apply transformations to tract masks | [`main`](https://github.com/gamorosino/app-apply-ants-transform/tree/main) | [doi:10.25663/brainlife.app.850](https://doi.org/10.25663/brainlife.app.850) | available |
| Apply transformations to T1w | [`main`](https://github.com/gamorosino/app-apply-ants-transform/tree/main) | [doi:10.25663/brainlife.app.848](https://doi.org/10.25663/brainlife.app.848) | available |

## Running the App

### On brainlife.io

Open the App at [doi.org/10.25663/brainlife.app.979](https://doi.org/10.25663/brainlife.app.979), choose `neuro/transform/nifti`, `neuro/tensor`, `neuro/noddi`, `neuro/anat/t1w`, `neuro/anat/t2w` from your project, set the parameters and press **Execute**. The results are saved to your project as `neuro/tensor`.

### Locally

You need [Singularity/Apptainer](https://apptainer.org/) (the App runs in `brainlife/ants:2.2.0-1bc`), [`jq`](https://jqlang.github.io/jq/).

1. Clone this repository and check out the App's branch (`git checkout diffmetrics`).
2. Create `config.json` with the paths to your inputs and the parameters:

   ```json
   {
       "warp": "/path/to/transform/warp",
       "inverse-warp": "/path/to/transform/inverse-warp",
       "affine": "/path/to/transform/affine",
       "moving_fa": "/path/to/moving_tensor/moving_fa",
       "moving_md": "/path/to/moving_tensor/moving_md",
       "moving_rd": "/path/to/moving_tensor/moving_rd",
       "moving_ad": "/path/to/moving_tensor/moving_ad",
       "moving_ndi": "/path/to/moving_noddi/moving_ndi",
       "moving_isovf": "/path/to/moving_noddi/moving_isovf",
       "moving_odi": "/path/to/moving_noddi/moving_odi",
       "t1": "/path/to/fixed_T1/t1",
       "t2": "/path/to/fixed_t2/t2",
       "interpolation": "Linear",
       "inverse": false,
       "affine_only": false,
       "fsl_transform": false
   }
   ```

3. Run `./main`.

## Troubleshooting

- **Error: No valid anatomical reference found in config.json**: Provide a T1w or T2w reference image.
- **Error: affine_only is false but appropriate warp is missing**: Provide a transform with the needed warp or set affine_only.

## Features

This app performs:
- Application of nonlinear warps or inverse warps
- Optional use of affine-only transformations
- Optional conversion from FSL (FLIRT/FNIRT) to ANTs-compatible format
- Flexible input/reference image handling (T1w, T2w, parcellation, mask, DWI)
- Interpolation options (Linear, NearestNeighbor, etc.)

The transformed output image is saved in standard NIfTI format.

## Inputs (`config.json`)

Example:
```json
{
    "warp": "transforms/warp.nii.gz",
    "inverse-warp": "transforms/inverse_warp.nii.gz",
    "affine": "transforms/affine.txt",
    "input_t1": "native/input_image.nii.gz",
    "interpolation": "Linear",
    "inverse": false,
    "affine_only": false,
    "fsl_transform": true,
    "t1": "anat/T1w.nii.gz",
    "t2": null,
    "parc": null,
    "mask": null,
    "dwi": null
}
```

## Field Descriptions

| Field           | Description                                                                 |
|----------------|-----------------------------------------------------------------------------|
| `warp`          | Path to nonlinear warp field                                                |
| `inverse-warp`  | Path to inverse warp field (used if `inverse=true`)                         |
| `affine`        | Affine matrix (FLIRT-style if `fsl_transform=true`)                         |
| `input`         | Image to transform                                                          |
| `interpolation` | Interpolation method (`Linear`, `NearestNeighbor`, etc.)                   |
| `inverse`       | If `true`, applies affine and warp in inverse mode  |
| `affine_only`   | If `true`, applies only affine and skips warps                              |
| `fsl_transform` | If `true`, converts affine and warp to ANTs-compatible format               |
| `t1`, `t2`, `parc`, `mask`, `dwi` | Reference image; first non-null used as target image     |

## Local Execution

1. Clone the repository:
```bash
git clone https://github.com/gamorosino/app-apply-ants-transform.git
cd app-apply-ants-transform
```

2. Prepare `config.json` as shown above.

3. Run the script:
```bash
./main
```

The script will:
- Convert FLIRT/FNIRT transforms if requested
- Apply affine/warp transforms with proper ordering
- Save output to `ants_transformed/t1.nii.gz`

## Citing

If you use this App, please cite it, the software it builds on and brainlife.io:

- Apply Transformations to Diffusion Metrics. brainlife.io App. [doi:10.25663/brainlife.app.979](https://doi.org/10.25663/brainlife.app.979)
- ANTs: Avants et al., NeuroImage (2011) [https://doi.org/10.1016/j.neuroimage.2010.09.025](https://doi.org/10.1016/j.neuroimage.2010.09.025)
- Jenkinson et al. FSL. *NeuroImage* 62:782-790 (2012). [doi:10.1016/j.neuroimage.2011.09.015](https://doi.org/10.1016/j.neuroimage.2011.09.015)
- Marcus et al. Informatics and Data Mining Tools and Strategies for the Human Connectome Project. *Frontiers in Neuroinformatics* 5 (2011). [doi:10.3389/fninf.2011.00004](https://doi.org/10.3389/fninf.2011.00004)
- Zhang et al. NODDI: Practical in vivo neurite orientation dispersion and density imaging of the human brain. *NeuroImage* 61:1000-1016 (2012). [doi:10.1016/j.neuroimage.2012.03.072](https://doi.org/10.1016/j.neuroimage.2012.03.072)
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods* 21:809–813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)
- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. *Scientific Data* 6:69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)

## Authors

Gabriele Amorosino
