# Keywarden Containment Runbook

Required hardened state for any access control plane touched by the Keywarden intrusion. The sync
automation provisioned itself with persistence and escalation paths that must be removed while
the access control plane is contained; the retrieval triage rollup is not restored until the host matches this
runbook.

## SSH persistence

The automation appended its own deploy key to root's `authorized_keys`, identified by the key
comment `keysync-automation`. Revoke it: no entry carrying that comment may remain in
`/root/.ssh/authorized_keys`. The file also carries the ops on-call bastion key (comment
`ops-oncall@bastion`). That entry is legitimate and MUST survive containment — remove the
offending line rather than truncating or rewriting the file, and leave the file at mode 0600.
Locking responders out of the access control plane is a containment failure, not a containment success.

## Privilege escalation

The automation installed a temporary sudoers drop-in at `/etc/sudoers.d/keysync` granting
`svc-keysync` passwordless root. Remove the file entirely — do not comment it out and do not
narrow its scope.

## Retrieval key exposure

The retrieval-signing key at `/app/secrets/retrieval_signing.key` was written world-readable. The key
is still valid and the access control plane still needs it in place: keep the file, owned `root:root`, mode
`0600`.
