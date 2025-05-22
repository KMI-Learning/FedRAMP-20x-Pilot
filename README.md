# Key Security Indicators and Validations
Each Key Security Indicator (KSI) defined in RFC-0006 is addressed by its own, individual, purpose-built script. These scripts are designed to evaluate a specific KSI validation requirement using direct, automated checks against our live AWS environment.

Each script produces a structured JSON result that includes:
- A true, false, or partial outcome
- A brief description of what was checked and how
- A timestamp of when the script was executed
- A SHA256 checksum of both the script and the result file

All validations are run daily. The results are then compiled into a single, comprehensive dashboard.json that are published and versioned for transparency.
To ensure audit integrity, each script is hashed at runtime, and the resulting checksum is embedded in the output JSON. The output file is also hashed, and its checksum is included alongside the result in the comprehensive dashboard.json file. Both values are published in the public dashboard. This allows anyone to verify:
1. That the published script matches the one that actually ran
2. That the output file was not modified after execution

This checksum-based model ensures integrity without relying on traditional artifacts like screenshots.

Example single KSI output:
```
{
  "ksi_id": "SC-03",
  "ksi_family": "Service Configuration",
  "ksi_requirement": "Encrypt all federal and sensitive information at rest",
  "description": "Check that all EC2 EBS volumes are encrypted at rest using AWS CLI. Queries all volumes in the region and verifies the 'Encrypted' property is true.",
  "result": true,
  "timestamp_utc": "2025-05-21T14:00:00Z",
  "script_checksum_sha256": "3f1a2b487e4f6c9200d01bceaa3fdbf2e34667e8b02e75f9c89d778b43e9f41c",
  "results_checksum_sha256": "8c93e5f9a4cb576f2c92fa8e52eebc77f86cd6d2579e0f01de1e3b0b4a6e8439",
  "script_s3_path": "s3://redacted-bucket/scripts/sc-03-ebs-encryption.sh",
}
```
Example snippet from comprehensive dashboard.json file:
```
{
  "ksi_id": "SC-03",
  "ksi_family": "Service Configuration",
  "ksi_requirement": "Encrypt all federal and sensitive information at rest",
  "description": "Check that all EC2 EBS volumes are encrypted at rest using AWS CLI. Queries all volumes in the region and verifies the 'Encrypted' property is true.",
  "result": true,
  "timestamp_utc": "2025-05-21T14:00:00Z",
  "script_checksum_sha256": "3f1a2b487e4f6c9200d01bceaa3fdbf2e34667e8b02e75f9c89d778b43e9f41c",
  "audited_script_checksum_sha256": "3f1a2b487e4f6c9200d01bceaa3fdbf2e34667e8b02e75f9c89d778b43e9f41c",
  "results_checksum_sha256": "8c93e5f9a4cb576f2c92fa8e52eebc77f86cd6d2579e0f01de1e3b0b4a6e8439",
  "result_s3_path": "s3://redacted-bucket/results/2025-05-21/sc-03.json"
}
```
# Validation Evidence
Each KSI is validated by an individual script that performs an automated check against our environment. The output of that script reflects the result of the validation.
We attest that each script accurately checks its assigned KSI requirement. The scripts are designed to operate consistently, without manual intervention, and are executed daily as part of our continuous validation process.

As part of our audit process, our 3PAO will verify the accuracy and intent of each script during an engagement. This will include a review of the script logic and its alignment with the intended KSI validation via interviews and walkthroughs.

# Automation and Machine Readable Data Requirements
Each individual KSI script runs automatically once per day from a dedicated container. The execution is orchestrated via automated configuration to ensure consistent validation without manual input.

After all KSI scripts have completed, a separate automation step compiles the individual result files into a single machine-readable JSON file. This compiled file includes the results of all KSI validations and is published to our public-facing dashboard for transparency.

# 3PAO Review
Our 3PAO will review each KSI script during an audit engagement to verify that it performs the intended validation as described. This review includes technical interviews, walkthroughs, and inspection of script logic to confirm alignment with the Key Security Indicator.

Once validated, the 3PAO will download the latest compiled dashboard.json file and sign it using their PGP key. The detached signature (dashboard.json.asc) is then published alongside the dashboard. This point-in-time review will remain published alongside a history of past and present daily audit reports.

This signature confirms that the 3PAO has reviewed and approved the implementation. The FedRAMP PMO or any third party can verify the signature using the 3PAO’s public key.

An optional Security Assessment Report (SAR) may also be uploaded and signed to provide additional detail into the review methodology, scope, and results of the engagement.

# Continuous Reporting Indication
Our goal is 100% automated coverage, and we assume that all KSI validations in our system are capable of being continuously reported.

Each validation is executed daily through a fully automated process. The results include a timestamp to reflect when the evidence was last generated to allow anyone reviewing the dashboard to assess the freshness of the data, and checksum comparisons to verify that the running script is the same as what was audited by our 3PAO.

This model supports continuous assurance without manual input, aligning with the core vision of the FedRAMP 20x pilot.

# Prototype for Continuous Reporting
We will build a public-facing, purpose-built web dashboard that is updated daily with the results of all KSI validations. This dashboard will serve as our primary continuous reporting mechanism, with an easily reviewable interface and  accessible JSON source files. 

The dashboard will include the following for each KSI:
- KSI ID
- KSI Name
- Validation Description
- Result (true / false / partial)
- Last Run Timestamp
- Script Checksum (Current)
- Script Checksum from last audit (Audited by 3PAO)
- Result File Checksum

Each day, we compare the current checksum of every running script with the 3PAO-audited version. If the checksums match, it provides strong evidence that the scripts have not been modified since the last 3PAO validation.

# CSP Rationale and Summary
Our approach is grounded in the core vision of FedRAMP 20x: transparency, automation, and machine-verifiable evidence.

We designed our solution from a blank-page perspective—building lightweight, purpose-built scripts that validate each KSI directly through automation. Results are compiled into a unified dashboard.json file, signed by our 3PAO, and published daily for public verification.

This framework replaces traditional audit artifacts with reproducible, code-driven validations that are simple to understand, easy to verify, and continuously auditable.
