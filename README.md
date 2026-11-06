# Chen-Dai Distance-Based Intelligent Prediction System for Breast Cancer Sentinel Lymph Node Status V1.0

The application estimates the probability that a newly diagnosed breast cancer
patient has sentinel lymph node (SLN) metastasis. It wraps the MAT (Multimodal
Attention Transformer) model, which fuses three imaging modalities (DCE-MRI,
mammography and ultrasound) with 35 clinical and 20 pathological features.

---

## Contents

| Item | Description |
|---|---|
| `Chen-Dai-Distance-SLN-Predictor-V1.0-Windows-x64.zip` | Portable Windows x64 application, 495 MiB |
| `sample-data/Case1`, `sample-data/Case2` | Two de-identified example studies with clinical workbooks |
| `README.md` | This guide |

The archive is self-contained. Python, PyTorch and the model weights do not need
to be installed separately.

**Release file:** download from the [Releases](../../releases) page.

| Property | Value |
|---|---|
| File name | `Chen-Dai-Distance-SLN-Predictor-V1.0-Windows-x64.zip` |
| Size | 519,069,395 bytes (495 MiB) |
| SHA-256 | `e5a321cc5f1e9e6e0910fbccaca035a4838079c32d7ccf72686906b0221cd447` |

---

## 1. System Requirements

| Item | Requirement |
|---|---|
| Operating system | Windows 10 or Windows 11, 64-bit |
| Architecture | x86-64 (x64). Windows on ARM is supported through x64 emulation |
| Memory | 8 GB minimum, 16 GB recommended |
| Free disk space | 2 GB (the archive plus the extracted folder) |
| Runtime | Microsoft Visual C++ 2015-2022 Redistributable (x64) |
| Network | Required for the first sign-in and for account registration |
| Display | 1280 x 800 or larger |

This release provides the Windows build. A macOS build is available from the
authors on request.

---

## 2. Download and Verify

1. Open the **Releases** page of this repository.
2. Download `Chen-Dai-Distance-SLN-Predictor-V1.0-Windows-x64.zip`.
3. Optionally verify the download before running it, in PowerShell:

```powershell
Get-FileHash -Algorithm SHA256 ".\Chen-Dai-Distance-SLN-Predictor-V1.0-Windows-x64.zip"
```

The hash must match `e5a321cc5f1e9e6e0910fbccaca035a4838079c32d7ccf72686906b0221cd447`.

4. Extract the archive to a local folder, for example `D:\SLN-Predictor`.
   Do not run the application from inside the ZIP file, and avoid network
   shares or synced folders as the extraction target.

After extraction you should see:

```
基于陈-戴距离的乳腺癌前哨淋巴结状态智能预测系统 V1.0\
├── 基于陈-戴距离的乳腺癌前哨淋巴结状态智能预测系统 V1.0.exe
└── _internal\
```

The `_internal` folder holds the runtime, the encrypted model asset and the Qt
libraries. Keep it next to the `.exe`. Copying only the `.exe` will not work.

---

## 3. First Launch

Double-click `基于陈-戴距离的乳腺癌前哨淋巴结状态智能预测系统 V1.0.exe`.

The first start takes 10 to 30 seconds while the runtime initialises.

If Windows SmartScreen appears, select **More info**, confirm the publisher
line, then select **Run anyway**. The executable is not code-signed with a
commercial certificate, so this prompt is expected for an unsigned build.

The application starts in **Simplified Chinese**. Section 4 explains how to
switch the whole interface to English.

---

## 4. Switching the Interface to English

Every label, dialog, message, and result page follows one global language
setting. Two entry points are available.

### 4.1 On the login window

1. Start the application. The sign-in window opens first.
2. At the top right of the window header, find the language list. It shows
   `简体中文`.
3. Click the list and choose **English**.
4. The header, both tabs (`Sign In`, `Register`), all field labels, the
   verification-code block, and every message switch to English immediately.

### 4.2 After signing in

1. Open **Settings** in the left navigation bar.
2. Under **Appearance**, locate the row labelled **Language**.
3. Change the value from `Simplified Chinese` to **English**.
4. The sidebar, page titles, clinical form, viewer controls, result pages and
   dialogs switch at once. No restart is needed.

### 4.3 Notes

- The language and theme choices are remembered for the current session. Verify
  the selector again after restarting if a different setting is required.
- The theme control beside the language list cycles through **Follow System**,
  **Dark Theme** and **Light Theme**. It is independent of the language.
- Patient data values are never translated. Only interface text changes.
- Every screenshot and label in the remainder of this guide uses the English
  interface.

---

## 5. Account Access

The application requires an account. Data processing and inference run locally;
the account service is used for authentication only.

### 5.1 Sign in

1. Open the **Sign In** tab.
2. Enter your registered username or verified email address.
3. Enter your password and select **Sign In**.

### 5.2 Register

1. Open the **Register** tab.
2. Enter a username (3 to 48 characters), an email address, and a password of
   at least 6 characters, then confirm the password.
3. Select **Send Code**. A 6-digit verification code is emailed to the address.
   Codes are valid for 10 minutes.
4. Enter the code and select **Verify and Register**.

### 5.3 Research-use consent

After the first successful sign-in, a **Usage Consent** window appears. Read the
research-use statement and select **I Have Read and Agree** to continue. The
same statement is always available under **Settings** > **About** >
**Research Use Statement**.

---

## 6. Input Data

A prediction requires all five image views with manually delineated masks, plus
the clinical and pathological fields.

### 6.1 Patient folder layout (NIfTI input)

```
PatientID/
├── {ID}_DCE.nii[.gz]
├── {ID}_DCE_GTV.nii[.gz]
├── {ID}_DCE_GTVnd1.nii[.gz]
├── {ID}_DCE_Center.nii[.gz]
├── MG/
│   ├── *CC*.nii[.gz]
│   ├── *CC*_GTV.nii[.gz]
│   ├── *MLO*.nii[.gz]
│   ├── *MLO*_GTV.nii[.gz]
│   └── *MLO*_GTVnd1.nii[.gz]
└── US/
    ├── {ID}_H.nii[.gz]
    ├── {ID}_H_GTV.nii[.gz]
    ├── {ID}_V.nii[.gz]
    └── {ID}_V_GTV.nii[.gz]
```

Notes on this layout:

- The DCE images and masks sit at the study root, not in a `DCE/` subfolder.
  Studies that keep DCE under `DCE/` are also accepted.
- `{ID}_DCE_GTVnd.nii[.gz]` may be added when the nodal-region contour is
  available. It is optional for prediction.
- `{ID}_DCE_Center.nii[.gz]` is required only to reproduce the MAT
  `location=0` branch, which runs `GTV → DCE_Center → GTVnd1`. The
  `location=1` branch needs only `GTV` and `GTVnd1`.
- `{ID}_DCE_N4.nii[.gz]` may be supplied as a precomputed bias-field-corrected
  volume. When present, the application loads it directly and skips N4
  correction, which makes the first run much faster.

### 6.2 Clinical workbook

A single Excel workbook may hold one row per patient. The application reads
every worksheet, detects the header row, and matches the image folder name to
recognised identifier columns (`Study ID`, `Patient ID`, hospital number or
medical record number). Leading zeros and punctuation differences are
tolerated; ambiguous matches are rejected instead of guessed.

Final ER, PR and HER2 interpretation columns take priority over raw percentage
or immunohistochemistry columns. An equivocal HER2 result of `2+` is left
blank rather than inferred.

**The clinical workbook does not need to contain the Chen-Dai distance or the
MAT location branch.** The application derives both from the DCE masks; see
Section 7.3.

### 6.3 Bundled sample studies

Two de-identified example studies are included in `sample-data/` so the software
can be exercised without collecting new cases.

| Study | Laterality | cT | Lesion site | DCE volume | Mask geometry |
|---|---|---|---|---|---|
| `sample-data/Case1` | Left | T1 | UOQ | 320 x 320 x 160 | matches source |
| `sample-data/Case2` | Right | T3 | UOQ | 448 x 448 x 112 | matches source |

Each study already contains all five image views, the `GTV`, `GTVnd1` and
`DCE_Center` masks, a precomputed `_DCE_N4` volume, and its own clinical
workbook. The workbooks carry the full clinical and pathological panel but omit
the Chen-Dai distance and the MAT location, which is the normal situation for a
clinical workbook. This means the distance has to be calculated in the
application, exactly as described in Section 7.3.

Both studies were validated with the application's own loader:

| Study | Modalities detected | Chen-Dai candidates |
|---|---|---|
| `Case1` | DCE, MG-CC, MG-MLO, US-H, US-V | `location=1` 88.884 mm, `location=0` 211.226 mm |
| `Case2` | DCE, MG-CC, MG-MLO, US-H, US-V | `location=1` 63.209 mm, `location=0` 171.094 mm |

Because each study has a `DCE_Center` mask, both MAT branches are mathematically
valid. The application therefore asks the operator to confirm which branch
matches the annotation definition used for that patient; it never picks one
silently. To predict without that prompt, add the original `location` value or a
distance to the workbook, as described in Section 7.3.

---

## 7. End-to-End Workflow

### 7.1 Single patient, NIfTI input

1. Select **Import** in the left navigation bar.
2. Stay on the **Single-Patient Import** tab.
3. Select **Browse…** and choose the patient folder.
4. Review the modality status shown for DCE, MG and US. Missing required views
   are listed explicitly and block prediction.
5. Select **View Images** to open the **Medical Image Viewer** and confirm that
   the images and existing masks are correct. This step is optional for a study
   that was already validated.
6. Fill in the **Clinical & Pathology Information** panel, or select the
   workbook import action inside that panel and confirm the proposed field
   mapping. The mapping dialog lists the MAT field, the source column, the
   parsed value and any warning before anything is written into the form.

### 7.2 DICOM input

DICOM import is an image conversion step only. It does not create tumour or
nodal contours.

1. On the **Import** page, select the folder that contains the DICOM series and
   select **Convert DICOM to NIfTI**.
2. For each patient, a series-selection dialog lists the candidate MR series
   ranked from DICOM metadata. Confirm the DCE series and the dynamic phase.
   The converter does not fall back to the largest or highest-scoring series.
   Cancelling the dialog cancels that conversion.
3. Wait for the conversion to finish. Slices are ordered from
   `ImagePositionPatient` and `ImageOrientationPatient`, then written in a
   stable LPS display frame without resampling.
4. Open **Viewer**, inspect the axial, coronal and sagittal planes, and use
   **Confirm DICOM Orientation** before drawing contours.
5. Delineate `GTV`, `GTVnd` and `GTVnd1` either in the built-in viewer or in an
   external tool such as ITK-SNAP or 3D Slicer, then save the masks beside the
   images.
6. Re-import the converted folder as a NIfTI study and continue with Section
   7.3.

### 7.3 Calculate the Chen-Dai distance

This step is mandatory and is the distinctive input of this system.

1. On the **Import** page, select **Calculate Chen-Dai Distance**.
2. The application reproduces the MAT centroid and physical-spacing
   calculation on the DCE labels and reports the result in millimetres.
3. If the workbook already carries a distance value, a comparison dialog shows
   the workbook value, the calculated value and the absolute difference. The
   calculated value is the one used for prediction.
4. If neither a distance nor a MAT location branch is present, the application
   evaluates the valid MAT branches and asks you to confirm the appropriate
   branch before prediction.

Prediction stays blocked until a calculated distance is present. Editing the
field by hand does not satisfy this check, because the value is recomputed
inside the prediction pipeline.

### 7.4 Run the prediction

1. Select **Run Prediction**.
2. The status bar shows a percentage-based progress indicator for the current
   stage, including DCE preparation, N4 bias-field correction, feature encoding
   and MAT inference. A cancel control is available while the job runs.
3. N4 correction runs on the first pass over a given DCE volume and is cached
   afterwards. Expect several minutes for the first patient on a laptop-class
   CPU; later runs on the same study reuse the cache.
4. The application moves to **Result** when inference completes.

### 7.5 Read the result

The **Prediction Result** page shows an animated probability gauge, the risk
band, the modality summary, and a table of every input value used.

| Probability | Risk band | Suggested action shown in the application |
|---|---|---|
| below 30% | Low Risk | Consider de-escalated axillary surgery |
| 30% to 60% | Medium Risk | Further clinical evaluation recommended |
| above 60% | High Risk | Standard axillary staging recommended |

Select **Export Reproducibility Bundle…** to write a ZIP that records the NIfTI
inputs, file hashes, encoded MAT features, preprocessing state, model asset
hash, runtime details and the prediction output for that case.

### 7.6 Batch prediction

1. On the **Import** page, switch to the **Batch Import** tab.
2. Select the directory that contains the patient folders, then select
   **Select Excel…** and choose the clinical workbook.
3. Review the pre-check counts: patients that are ready, patients with
   insufficient fields, and patients whose identifier could not be matched.
4. Confirm the representative field mapping, then select **Run Batch
   Prediction**. Each patient is re-validated before inference.
5. Review the **Batch Prediction Results** table. It records field coverage,
   source worksheet and row, parse warnings, and the error reason for any
   patient that could not be scored.
6. Select **View Selected Images** to inspect a row, or **Export CSV** to keep
   the quality annotations alongside the probabilities.

### 7.7 Walkthrough with the bundled sample data

The steps below produce a full prediction for `sample-data/Case1`. `Case2`
works identically.

1. Copy `sample-data/Case1` to a local folder, for example `D:\SLN-Data\Case1`.
   Running from a local disk avoids file-locking problems on network shares.
2. Launch the application and sign in.
3. Open **Import**, keep **NIfTI** selected, and stay on the
   **Single-Patient Import** tab.
4. Select **Browse…** and choose the `Case1` folder.
5. Confirm that the `DCE`, `MG` and `US` badges are all marked as available.
6. Select **View Images** and check that all five views render. The DCE panel
   should show the study without waiting for N4 correction, because
   `Case1_DCE_N4.nii.gz` is already present in the study.
7. Return to **Import**. Import the workbook that ships with the study,
   `Clinicopathological.xlsx`, and confirm the proposed field mapping. The
   matched workbook has a high completeness score and fills age, laterality,
   multifocality, lesion site, cT, axillary ultrasound, calcification, breast
   density, ultrasound and mammography and MRI sizes, histological type and
   grade, ER, PR, HER2, Ki67 and LVI.
8. Select **Calculate Chen-Dai Distance**. Because the workbook carries neither
   a distance nor a location, and the study has a `DCE_Center` mask, a
   confirmation table appears with two MAT branches:

   | MAT branch | Distance | Calculation path |
   |---|---|---|
   | `location=1` | 88.884 mm | GTV → GTVnd1 |
   | `location=0` | 211.226 mm | GTV → DCE_Center → GTVnd1 |

9. Select the branch that matches the annotation definition for this study and
   confirm. The chosen value is written into the clinical form and becomes part
   of the model input.
10. Select **Run Prediction**. Progress is reported in the status bar through
    DCE preparation, feature encoding and MAT inference. Because the study
    already contains `Case1_DCE_N4.nii.gz`, the N4 step is skipped and
    inference starts immediately.
11. The **Prediction Result** page shows the probability gauge, the risk band,
    the modality summary and every input value used. Select **Export
    Reproducibility Bundle…** if a record of that run is needed.

To score both studies as a cohort, point **Batch Import** at the folder that
contains `Case1` and `Case2`. Batch mode expects one workbook for the cohort, so
either merge the two single-row workbooks into one sheet, or select
`Case1/Clinicopathological.xlsx` after adding a `Case2` row to it. Each row is
validated again before inference, and the confirmation step in Section 7.3 is
requested per patient when the branch is ambiguous.

---

## 8. Manual Delineation in the Image Viewer

1. Open **Viewer**. The overview shows DCE-MRI, mammography CC, mammography MLO
   and ultrasound.
2. Select **Focus** on the DCE panel for linked axial, coronal and sagittal
   review.
3. Choose a label from `GTV`, `GTVnd` or `GTVnd1`.
4. Pick the brush or eraser and adjust the brush width with the `-` and `+`
   controls next to the size value.
5. Draw on any DCE plane. Undo and redo are available, and the current mask can
   be copied to the adjacent slice.
6. Select the save control, or press `Ctrl+S`, to write the labels. The
   application writes `{ID}_DCE_GTV.nii.gz`, `{ID}_DCE_GTVnd.nii.gz` and
   `{ID}_DCE_GTVnd1.nii.gz` while preserving the source NIfTI geometry.
7. Switch to another modality to draw 2-D labels where the protocol requires
   them.

Keyboard shortcuts in the viewer:

| Key | Action |
|---|---|
| `B` | Brush |
| `E` | Eraser |
| `F` | Region grow |
| `1` / `2` / `3` | Select the `GTV`, `GTVnd` or `GTVnd1` label |
| `[` / `]` | Decrease or increase the brush width |
| `Ctrl+Z` / `Ctrl+Y` | Undo or redo |
| `Ctrl+S` | Save the current labels |

---

## 9. Settings Reference

| Section | Setting | Purpose |
|---|---|---|
| Appearance | Theme | Follow System, Dark Theme or Light Theme |
| Appearance | Language | Simplified Chinese or English |
| Image Preprocessing | N4 Bias-Field Correction | Always required, shown as `Required` |
| Diagnostics & Logs | Log Privacy Mode | Detailed diagnostics or redacted diagnostics |
| Account | Current Account | Signed-in identity |
| Account | Change Password | Requires the current password |
| Account | Sign Out | Clears the session on this device |
| About | Research Use Statement | Full research-use declaration |

The **Activity Log** page keeps a chronological record of conversions,
calculations, predictions, and navigation events. It is the first place to
check when a step does not behave as expected.

Redacted log mode hides patient identifiers, file paths, email addresses and
tokens from new entries. Detailed mode should only be used on a controlled
local workstation.

---

## 10. Troubleshooting

| Symptom | Cause and resolution |
|---|---|
| `Windows protected your PC` on first launch | Unsigned executable. Select **More info**, then **Run anyway** |
| The application closes immediately after launch | The folder was not fully extracted, or `_internal` is missing. Re-extract the complete archive |
| `VCRUNTIME140.dll was not found` | Install the Microsoft Visual C++ 2015-2022 Redistributable (x64) |
| Sign-in fails with a network message | Check the internet connection and any institutional firewall or proxy |
| `Email has not been verified` | Complete registration with the emailed code, then sign in again |
| Prediction blocked with `Chen-Dai Distance Required` | Run **Calculate Chen-Dai Distance** again after any change to the DCE masks |
| Prediction blocked with an input checklist | One or more required views or masks are missing, empty, or have mismatched array dimensions |
| N4 correction takes several minutes | Expected on the first pass. The result is cached per study, so subsequent runs are faster |
| DICOM conversion produces `US_###` files | The ultrasound series had no reliable horizontal or vertical metadata. Rename them to `{ID}_H` and `{ID}_V` after visual review |
| Batch row shows a matching error | The folder identifier matched several workbook rows. Make the identifier unique in the workbook |

Application logs are written to a hidden folder in the user profile:

| Platform | Location |
|---|---|
| Windows | `%USERPROFILE%\.breast_cancer_node_status_prediction\logs` |

Attach the relevant log file when reporting a problem.

---

## 11. Intended Use and Limitations

This tool is intended for academic and research use only. It is not a medical
device, has not been cleared or approved by any regulatory body, and must not be
used as a substitute for clinical judgement. Predictions are model estimates
derived from a breast cancer cohort and may not apply to an individual patient.
All output should be interpreted by a licensed clinician.

The application does not replace radiologist review of DICOM series selection
or of manually delineated contours. Automated ranking of DCE candidates is a
selection aid only.

The studies under `sample-data/` are de-identified research examples shared so
that the software can be reproduced. They carry a study identifier, image
volumes, contours and the clinical/pathological panel only. They contain no
name, hospital number, medical record number, date of birth or acquisition date.
They are provided for software verification and method reproduction, not for
clinical use or for re-identification.

---

## 12. Distribution

This repository distributes a compiled binary for peer review. Source code is
not included, and no licence to modify, redistribute, sublicense or incorporate
the software into another product is granted. All rights remain with the
authors.

For questions about the manuscript, the model, or access to a macOS build,
contact the corresponding author through the journal's editorial system.
