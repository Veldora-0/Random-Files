# NetSentinel Final Report Audit

**Review date:** 16 November 2026  
**Repository inspected:** `/home/debian/Desktop/NetSentinel` (local working tree, branch `main`)  
**Document page count:** 28 pages (Letter)  
**Scope:** report and evidence review only; application source was not changed.

## Summary

The report was reorganized as a Full Stack Project report using the supplied college template’s Letter page geometry and page-number treatment. The existing technical narrative, diagrams, dashboard screenshots, test scenarios, reference list, and useful source excerpts were retained where supportable. The required chapter sequence is present, with the research-based alternative omitted.

The material change is in the Results and Performance Evaluation sections. The repository contains the synthetic end-to-end validation report, but it does not contain the CTU-IDSEVAL-6 inputs or a real-dataset metric output, nor retained threshold-sweep or subsystem benchmark result files. Those draft numerical claims are now explicitly unverified and are not repeated as established results. The CTU-IDSEVAL-6 Zenodo citation itself was checked and corrected; its existence does not verify NetSentinel’s claimed evaluation procedure or measurements.

## Structural and Formatting Corrections

- Applied the required five-chapter Full Stack Project structure: Introduction; Software Requirement Specification; System Design; Implementation and Results; Conclusion.
- Retained the requested SRS subsections, use cases, sequence diagrams, architecture/class/DFD/activity/ER/database-schema content, implementation snapshots, test cases, results, comparison, future directions, appendix, and references.
- Removed the Research-Based Project alternative and kept the Full Stack Project organization.
- Added chapter page breaks, continuous page flow within chapters, Letter page size, the template’s margins, and its right-aligned page-number field.
- Preserved the supplied report’s actual diagram and interface screenshot assets. Captions remain associated with the corresponding extracted figures; no replacement dashboard images were created.
- Removed the blank page created during the first rendering iteration. The final rendered PDF has 28 pages and no blank page.
- The DOCX is editable. Tables from the PDF text extraction are represented as editable text rather than reconstructed as native Word tables; review any table that will be heavily edited later.

## Technical Claims Confirmed Against the Repository

| Area | Confirmed implementation facts |
|---|---|
| Capture and parsing | Linux `AF_PACKET` raw-frame capture; parser handles Ethernet ARP, IPv4 and IPv6, TCP, UDP, ICMP and ICMPv6. A permission-denied capture state is represented. |
| Network rules | Port scan: 15 distinct destination ports in 10 s, MEDIUM; SYN flood: 50 SYN packets in 5 s, HIGH; NULL and XMAS flag detections, HIGH; ICMP sweep: 10 targets in 10 s, MEDIUM. Default rule alert cooldown is 30 s; ICMP sweep cooldown is 60 s. These are configurable. |
| ARP | Stateful IP-to-MAC conflict monitoring and MAC-to-multiple-IP identity conflict monitoring; default conflict threshold is three IPs, default cooldown 60 s. It does not rewrite kernel ARP tables. |
| Host monitoring | SSH brute-force threshold defaults to five failures in 60 s. Process monitoring flags executable paths in temporary writable locations or deleted/unlinked binaries. FIM emits create, delete, replace, content modification and metadata-change event types. |
| Machine learning | Unsupervised Isolation Forest. The canonical feature vector has 13 ordered traffic features. Default traffic windows are 5 s; baseline target/minimum is 512 windows (about 42 min 40 s); production settings are 100 trees, `max_samples="auto"`, `contamination="auto"`, seed 42, threshold 0.50. Model metadata checks include feature compatibility. |
| Risk scoring | Rule/ML weights default to 0.65/0.35; severity base values are LOW 0.20, MEDIUM 0.40, HIGH 0.70, CRITICAL 0.90. Repeat activity adds 0.05 per repeat, capped at 0.20 over 60 s. ML-only risk is weighted at 0.35. Score actions map at 0.30, 0.60 and 0.80. Risk-engine network/host correlation adds 0.10, capped at 0.20. Threat-intelligence modifiers are +0.15 malicious, +0.05 suspicious and +0.02 conflicting, halved when stale. Incident-level boosts separately add +0.10 cross-domain and +0.05 multi-vector, capped at +0.20. |
| Incidents | Active OPEN/ACKNOWLEDGED incidents are keyed and grouped within a 300 s window; incident risk and severity are monotonic while active. Incident status supports the documented lifecycle. |
| Firewall | Managed `NETSENTINEL` chain; automatic firewall and auto-block defaults are off, dry-run defaults on, and block duration defaults to 300 s. Safety checks cover loopback, multicast, unspecified, reserved, global broadcast, local addresses and configured allowlists. A recommendation to block is distinct from an actual kernel rule change. |
| Authentication and roles | Password hashes use Werkzeug’s password-hashing API. Bearer tokens are generated randomly; only SHA-256 token digests are persisted. Viewer has read access; Analyst adds incident management and threat-intelligence lookup; Administrator adds firewall, FIM, user-management and system permissions. |
| Persistence and UI | SQLite through Flask-SQLAlchemy; backend routes and Socket.IO feed a React dashboard. The schema includes security events, risk assessments, firewall actions, telemetry, incidents/evidence, FIM baselines, threat-intelligence cache/observations and auth records. |

Repository evidence was read from `backend/capture.py`, `backend/parser.py`, `backend/config.py`, `backend/detector.py`, `backend/arp_detector.py`, `backend/host/`, `backend/ml/`, `backend/risk_engine.py`, `backend/incident_manager.py`, `backend/firewall.py`, `backend/auth/`, `backend/database.py`, `frontend/`, and the checked-in data/model metadata.

## Numerical Results and Evaluation Evidence

### Confirmed from retained artifacts

- `reports/validation_report.json` and `.md` record **13 of 13** synthetic end-to-end scenarios as PASS, **100.0%**, with a total reported duration of **1,430.26 ms**. The scenarios use synthetic traffic and keep the firewall disabled. This is not evidence of a live attack or live firewall block.
- The current test suite collection reports **459 tests collected** (`pytest --collect-only -q`). A full test run was attempted but did not complete in this review environment. Therefore the audit and revised report do not claim a current all-tests-passed result.
- The tracked model metadata records 512 training samples, 13 features, 100 estimators, automatic max-sample and contamination settings, and random state 42.

### Marked unverified in the revised report

- CTU-IDSEVAL-6 confusion matrix and metrics (accuracy, precision, recall, F1, ROC-AUC, PR-AUC, false-positive and false-negative rates): dataset files, project-specific evaluation inputs and result outputs were not present locally.
- Dataset train/test separation, feature compatibility and label processing in the NetSentinel evaluation: not verifiable from the checked-out source and retained artifacts. The dataset’s Zenodo metadata and DOI are verified in reference [20].
- Synthetic threshold-sweep numbers and benchmark/resource measurements: evaluator/benchmark code exists, but the corresponding retained result outputs were absent. The values in the draft were removed as verified claims.
- Current complete pytest pass count: not verified because execution did not complete. Representative test-case statuses are labeled accordingly.

## Reference and Citation Corrections

- Kept numeric citation numbering for the references retained in the report.
- Corrected [20] to include the creators shown in the Zenodo record: S. Garcia, V. Valeros, and G. Alya; title; Zenodo; 2026; DOI `10.5281/zenodo.21027042`.
- Confirmed the record title, creators, publication date and DOI on the [Zenodo record](https://zenodo.org/records/21027042). The record’s existence confirms citation metadata only; it does not independently substantiate the report’s claimed NetSentinel scores.
- Did not add unverified papers or citations.

## Remaining Review Items

1. To reinstate CTU-IDSEVAL-6 results, retain the exact downloaded dataset/version, checksums, preprocessing and window-building configuration, train/test manifest, confusion matrix, metric output, and executable command/script used to produce them. Then recalculate all metrics and update every occurrence consistently.
2. To reinstate performance and threshold-sweep numbers, retain the raw evaluator output, environment details, sample counts, seeds, and benchmark/resource-monitor reports.
3. Run the complete 459-test suite in a time window that permits completion and attach the full test log before stating a pass rate.
4. Review the converted text-based tables in Word if native table-cell editing or exact column widths are required.

## Final QA

- Reopened and rendered the final DOCX with the document renderer.
- Inspected contact sheets covering all 28 rendered pages, plus the first page and figure-heavy pages at full size.
- Confirmed Letter page size, 28-page count, consistent page numbers, no blank page, and readable figures/screenshots with no obvious clipping or distortion.
- Confirmed the final report contains no draft CTU metric values or unverified throughput/memory figures as established results.
- Confirmed the repository worktree has no application-code modifications; only the separate `final_report/` deliverables were added.
