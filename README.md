# CityEdge-Reports

This repository contains the CityEdge Sports Academy normalization report and supporting files for KASHAN ALI (Student ID 66066).

Files included:
- CityEdge_Normalisation_Report_KASHAN_ALI_66066.md — full report in Markdown
- (Optional) CityEdge_Normalisation_Report_KASHAN_ALI_66066.docx.b64 — base64-encoded Word doc (if provided)

## How to create the repo and upload files (web UI)

1. Sign in to GitHub.
2. Click "New" to create a repository.
   - Repository name: CityEdge-Reports
   - Description: CityEdge Sports Academy normalization report
   - Visibility: Public (or Private if you prefer)
3. Create the repo.
4. Click "Add file" → "Create new file".
5. Name the file `CityEdge_Normalisation_Report_KASHAN_ALI_66066.md` and paste the Markdown content from the supplied file.
6. Commit the file.
7. Repeat for `README.md`.

## How to push files using git (local)

Run these commands in your terminal (replace email/name/config as needed):

```bash
git config --global user.name "YOUR_GITHUB_USERNAME"
git config --global user.email "YOUR_EMAIL"
mkdir CityEdge-Reports
cd CityEdge-Reports
git init
# create the files locally (use a text editor to paste content)
git add CityEdge_Normalisation_Report_KASHAN_ALI_66066.md README.md
git commit -m "Add CityEdge normalization report for KASHAN ALI"
# create repo on GitHub via website or GitHub CLI, then:
git remote add origin https://github.com/YOUR_USERNAME/CityEdge-Reports.git
git branch -M main
git push -u origin main
