---
name: Ingest collection data and run analysis
description: Upload SharpHound/AzureHound collection files to BloodHound and trigger graph analysis.
api: openapi/specterops-bloodhound-openapi.json
operations: [CreateFileUploadJob, UploadFileToJob, EndFileUploadJob, StartAnalysis, GetDatapipeStatus]
---

# Ingest collection data and run analysis

Use this skill to load collector output into BloodHound and kick off analysis.

## Auth
JWT bearer or HMAC-signed request (see `authentication/specterops-authentication.yml`).

## Steps
1. `CreateFileUploadJob` — `POST /api/v2/file-upload/start` to open an ingest job; capture the
   `file_upload_job_id`.
2. `UploadFileToJob` — `POST /api/v2/file-upload/{file_upload_job_id}` for each SharpHound/AzureHound
   JSON/zip file. Check accepted types with `ListAcceptedFileUploadTypes`.
3. `EndFileUploadJob` — `POST /api/v2/file-upload/{file_upload_job_id}/end` to close the job and queue
   processing.
4. `StartAnalysis` — `PUT /api/v2/analysis` to trigger graph analysis (or rely on scheduled analysis).
5. `GetDatapipeStatus` — `GET /api/v2/datapipe/status` and poll until ingest + analysis complete.

## Notes
- Errors return the `api.error-wrapper` envelope with a `request_id` for support.
- Back off on HTTP 429; large ingests can take time — poll the datapipe rather than assuming completion.
