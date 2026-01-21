# Generative AI for Medical Imaging

## Background

- AI models have great potential in healthcare.
- However, there is a shortage of (high-quality) training data, particularly 3D scans from pathological cases.
- Our project aims to use generative AI to address this gap through synthetic anomaly generation.
- Specifically, our generated data with simulate realistic pathological growth over time.
- The project be guided by medical experts experienced in interpreting similar images, ensuring realistic and hight quality output data.
- The resulting synthetic dataset can then be used to train/evaluate medical AI models.

## Stage 1 - Presentation

We create and present a 3-5 minute video pitch with slides, covering:
- Introduction (project, members, mentor)
- Project statement
- Current landscape and existing solutions (with references)
- Motivations
  - Why this matters
  - Who it benefits
  - Potential impacts
- Data considerations (sources, structure, and limitations)
- Proposed solution
  - Overview
  - I/O
  - Role of generative AI
- Tools and technologies
- Timeline, risks and constraints

### Research:

**Models to look into**

- RoentGen-v2
- MediSyn
- FreeTumor
- MINIM

**Terminology**:

`Diffusion model` (LDM)

`Generative adversarial network` (GAN)

`Generalist Medical AI` (GMAI)

`Longitudinal growth`
>Multiple scans (of the same patient) over time (e.g. baseline, 3 months, 6 months). This means the anatomy (mostly) stays the same and only the pathology changes.

`Temporal diffusions` / `spatiotemporal modelling`

`Privacy enhancing technology` (PET)

`Latent diffusion`

`Segmentation mask`

`Visual Turing Test`

`Conditional generation` (mask/bbox + context)

`Deformation / growth field`

`Diffusion inpainting`

`Modality`
>e.g. CT, MRI

**Motivations**

*Models requires thousands of scans to train.*

1. Rare pathologies / specific growth stages are often missing.

>We can fill in the gaps of the training data (i.e. not necessarily become the entire dataset).

2. The available data may be biased (e.g. most of the scans coming from a specific segment of society). 

>We can boost the amount of data from underrepresented groups. (see RoentGen-v2 for precedent).

3. Sharing data between institutions can violate laws like GDPR and HIPAA.

>Our synthetic data are essentially fake, so these restrictions (might) not apply.

**Data**

`Sources`

**Healthy**
- *HEALTHY-TOTAL-BODY-CTs* (30 scans)
- *TotalSegmentator* (1200 CTs, 104 anatomical structures). On GitHub/Zenodo

**Pathological**
- *BRATS* (brain tumours)
- *LUNA16* (lung nodules)
- *The Cancer Imaging Archive*
- *Lung Image Database Consortium* (1000+ cases of lung nodules with annotations)
- *NLST-New-Lesion-LongCT* (highlights new lesions that appear between timepoints)

*Kaggle* also hosts medical datasets.

`Format`
Usually *DICOM* and *NIfTI* formats are used for 3D imaging.

As these are slow to work with, latent diffusion is often used to compress the data.

**Solution**

*Input*

- Healthy 3D scan
- Prompt (e.g. "simulate a 2cm growth in the upper lobe with a 6-month growth pattern")

*Process*

1. Seed selection (choose a healthy scan)
2. Anomaly injection ("paint" a pathology over the anatomy)
3. Anatomical consistency (check the biological boundaries of the pathology have been respected)

*Output*
- A synthetic 3D scan with segmentation marks (to be used for AI training)

The output can include:
1. A "multi-timepoint 3D volume"
2. The segmentation mask for each timepoint
3. Relevant metadata (e.g. growth parameters, prompts, etc.)

**Constraints**

1. Diffusion models have been shown to sometimes "memorise" their training data. This would risk leaking real patient data.

2. The AI might hallucinate a medically impossible artifact that looks very real.

>This can be minimised by using a hybrid approach (opposed to a purely generative model). For example, combining 1. copying real pathologies on healthy anatomy, 2. constraining diffusion to a small region, and 3. "normal" (**not** pathological) biological growth models.

3. The synthetic dataset may cause the model to produce synthetic images.
>This can be mitigated by comparing our output to real pathology scans / a model trained on real data.

4. Human experts must be in the loop to perform visual turing tests to ensure quality.

5. The model might create implausible progression.
>We can define simple rules to minimise these errors

6. If the seed data is already biased, the synthesis data may just amplify that bias.
>We  should find datasets with good coverage.

**Technologies**

*Frameworks*
1. PyTorch
2. TensorFlow
3. MONAI
4. *ITK-SNAP*

*Models*
1. Stable diffusion (fine-tuned)
2. ControlNet

*Compute*
1. **Kaggle kernels** - 30 hr/week for free (NVIDIA P100 or T4)
2. **Google colab** - sufficient for small tests, but 12.5GB RAM may be too low for 3D volumes
3. **Amazon SageMaker Studio Lab** - 15GB persistent storage

We can also check *Microsoft for startups*, *AWS activate*, and *Google for startups* to see if we're eligible to free credits.


**Timeline**

We must allot time to
1. Data curation
2. Model training
3. Expert validation

**Success metrics**

1. Image realism
>Is pathology is plausible? Can a (blinded) radiologist to tell if the image is real vs. synthetic.

2. Longitudinal consistency
>Did the anatomy remain aligned? Did the volume increase plausibly?

3. Downstream utility
>Can our data be used to improve a models performance?

### Plan

We should pick one small subset of data to focus on. For example:
- Glicomas in brain MRIs (large labeled public datasets)
- Lung nodules in chest CTs (measurable, conceptually clear)

We should find anomalies which have many factors. For examples:
1. Location
2. Size
3. Growth rate
4. Shape
5. Intensity

We should also decide how many timepoints we will generate.

We should prefer generating a deformation field, rather than generation a whole new scan.

It might be worth sticking to one file format + preprocessing steps.

We should consider how to 'use' the medical expert. For example:
1. Brief explanations of what good/bad synthetic images look like.
2. Define pathology progression archetypes.
3. Review generated images.

