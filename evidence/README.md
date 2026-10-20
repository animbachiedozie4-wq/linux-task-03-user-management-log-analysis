# Evidence Notes

Use this folder for supplementary evidence if needed, such as terminal transcripts or verification notes. Do not add passwords, tokens, or personal information.

Suggested checks:
- `wc -l ~/logs/sys_events.log`
- `ls -l ~/logs`
- `ls -l ~/logs/confidential.txt`
- `id forensic01`
- `id guest1`
- `getent passwd guest2` after deletion (should return no matching account)
