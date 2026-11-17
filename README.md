# MediDigit: Medical Record Digitiser

MLCC Activity 2 mini project (25MCASC303), MCA Semester III,
JAIN (Deemed-to-be University), Bengaluru. Academic year 2026-2027.

Team: Abhishek Kumar (25MCAR0141), Adithya Tej B M (25MCAR0142),
Surya R M (25MCAR0164). Faculty: Dr. N. Gobi.

## Problem

Doctors cannot access a patient's medical history across hospitals. Records
exist as paper slips, printed reports, and scanned pages held in separate
hospitals. This project covers the digitisation step: turn a record image
into searchable text, identify what kind of record it is, and keep it in one
place where the next doctor can find it by patient name.

## Scope

Module 1 only. Google Colab only, as required by the faculty. No cloud
integration, no Firebase, no mobile app. No live Google Cloud Vision API or
Natural Language API calls, no API key, no billing. Those two APIs are
explained as Module 1 theory only. Synthetic data and test samples only.
No real patient records. No diagnosis, no treatment advice. Classification
and extraction only, not for clinical use.

## Video



<video src="https://github.com/user-attachments/assets/e31ca165-9209-4600-b6c2-f4e62e65219e" data-canonical-src="https://github.com/user-attachments/assets/e31ca165-9209-4600-b6c2-f4e62e65219e" controls="controls" muted="muted" class="d-block rounded-bottom-2 border-top width-fit" style="max-height:640px; min-height: 200px"></video>


64-second narrated walkthrough: the problem, the pipeline, and the
results. Also available as [`MediDigit-Explainer.mp4`](MediDigit-Explainer.mp4).

## How it works

One Colab notebook, `MediDigit.ipynb`, 22 cells, runs top to bottom.

1. Upload one or more record images in the Upload cell. A tick box selects
   handwritten mode.
2. The notebook mounts Google Drive and creates `MyDrive/MediDigit`.
3. OCR: PaddleOCR first, preprocessed Tesseract second. The result with
   more words is kept. If handwritten mode is on, TrOCR
   (`microsoft/trocr-base-handwritten`) reads the page line by line and its
   text is stored only when it reads more words than the printed-text OCR.
4. The record type is found by keyword rules (`classify_record`): one of
   Prescription, Lab Report, Discharge Summary, or Imaging Report.
   Confidence is the winning type share of all matched keywords. No keyword
   match gives type "Not found", confidence 0.0. There is no trained model.
5. Fields (patient name, age, date, medicines, lab results, doctor and
   hospital, key findings, allergies, doctor advice, safety checks) are
   extracted with regular expressions. Anything not stated is "Not found",
   never invented. Patient name comes from the uploaded image only, else
   "Not found".
6. Every record is saved to a SQLite database (`medidigit_records.db`) and
   to one formatted Excel sheet (`medidigit_records.xlsx`, 13 columns) in
   `MyDrive/MediDigit`. The sheet is the only output. A history lookup by
   patient name returns all past records, newest first.

## Run it

1. Open `MediDigit.ipynb` in Google Colab.
2. Run all cells top to bottom.
3. Allow Google Drive access when asked. Upload a record image in the
   Upload cell. Tick the handwritten box only for handwritten records.
4. The Excel sheet appears in `MyDrive/MediDigit/medidigit_records.xlsx`.

## Files

- `MediDigit.ipynb`: the Colab notebook (Module 1 only, 22 cells).
- `MediDigit-Activity-2-Report.pdf`: the Activity 2 report (15 pages).
- `MediDigit-Explainer.mp4`: 64-second narrated explainer video.
- `video-thumbnail.png`: poster frame for the video preview.

## Results

Keyword-rule classification on the test images: three lab reports
(confidence 0.88 to 1.00) and one handwritten prescription (confidence 1.00)
identified correctly. Two "Not found" results are correct outputs: an
ultrasonography report whose OCR text held no imaging keyword, and a medical
certificate, which is not one of the four record types. On a real handwritten
prescription the OCR text was garbled and the date and medicine fields were
Not found. TrOCR took 454 seconds for 50 lines on CPU, so it is off by
default.
