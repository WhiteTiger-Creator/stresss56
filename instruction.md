# Keywarden key-access intrusion containment

Act as the security operations engineer responding to the Keywarden intrusion. There are three deliverables.

**Contain the host** per `/app/docs/containment_runbook.md`: revoke the automation's rogue SSH key from root's `authorized_keys` without locking out the ops on-call bastion key, delete the passwordless sudoers drop-in, and restrict the world-readable retrieval-signing key under `/app/secrets` to root-only (keep the key itself).

**Repair the triage rollup** at `/app/workflow/export_report.py`, which turns raw retrieval telemetry into responder escalation work. It must write three files under the output directory: the summary `summary.json`, the per-vault map `vault_matrix.json`, and the compact JSON-lines queue `escalated.jsonl`.

**Create** `/app/retrieval_audit.py` with two subcommands. `diagnose --dossier PATH --report PATH` writes a stateless report of the six known defects to `--report`; it accepts only those two flags and exits non-zero, writing no report, if given any other (such as `--input`). `repair --output-dir PATH` (default `/app/output`) rebuilds the workflow from the frozen snapshot `/app/workflow/.export_report.original` (leave it unmodified), overwrites the active `export_report.py`, reruns triage, and writes `diagnosis.json` and `repair_audit.json` beside the three files.

Every governing value — normalization, `retrieval_id` dedupe tie-breaks, dismissal-override windows, the near and wide probes, chain correlation and reach, and the escalation ledger — lives only in the dossier `/app/incident/export_dossier.md`, where a later dated ruling supersedes an earlier one. The exact schemas, key sets, digest payloads, checksum serialization, and the per-issue evidence rules are pinned in `/app/docs/report_spec.json`, with `/app/docs/output_contract.md` as the implementation guide. Output is judged on behaviour: identical on reruns, and correct on an alternate retrieval stream it has never seen.
