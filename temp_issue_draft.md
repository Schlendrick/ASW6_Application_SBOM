# Add support for SBOM generation in CI/CD pipelines

## Problem Statement

We need to run the SBOM script on an automated CI/CD pipeline hosted on our GitLab server. However, the GitLab runner environment does not have:
- AS6 (Automation Studio 6) installation available
- Access to the build files locally

This creates a blocker for automating SBOM generation in our pipeline environment.

## Current Limitations

- The build environment differs from the development environment
- AS6 is not installed on the CI/CD runner
- Dynamic detection of B&R-supplied packages currently depends on accessing the local AS installation to determine if a library is actually delivered by B&R or not
- This information cannot be statically maintained (lists, lookups) as it varies from version to version
- Without access to the AS installation, we cannot reliably determine package origins or other critical SBOM data

## Proposed Solutions

While a simple command-line parameter to override the Automation Runtime version might seem viable, it would not solve the core problem: reliably identifying which libraries are B&R-supplied packages without access to the AS installation.

However, there are potentially more robust approaches:

1. **Minimal Folder Structure Extraction**
   - Full AS installation is not required on the pipeline server
   - Extract only the necessary folder structures from a valid AS installation on a development machine
   - Copy these essential data structures to the pipeline environment
   - This would allow the SBOM tool to perform necessary checks without a full AS installation

2. **Automated Reference List Generation**
   - Generate reference lists (B&R package metadata, version mappings, etc.) from a valid AS installation
   - Automate this generation process from a development machine
   - Provide these reference lists to the pipeline environment
   - Update frequency could be tied to AS version releases or scheduled runs

3. **Hybrid Approach**
   - Combine automated reference list generation with minimal folder structure extraction
   - Use generated lists as a fallback mechanism
   - Maintain reliability by validating against actual AS installation data when available
   - Avoids maintaining manual lists while reducing reliability concerns

## Questions for Discussion

1. Has this use case (automated SBOM generation in CI/CD pipelines) been encountered before?
2. Is there an existing workaround or pattern used in similar scenarios?
3. Which of the proposed approaches (minimal folder extraction, automated reference lists, or a hybrid solution) would be most feasible?
4. Would it be possible to support both local AS installations and CI/CD environments through a flexible configuration approach?

## Notes

- A simple parameter-based approach would not be sufficient as it cannot reliably determine B&R package origins without access to installation metadata
- The solution should maintain the current reliability and accuracy of B&R package detection
- Community contributors' insights on technical feasibility and preferred implementation approach would be valuable
