# Required Explanations

## What does `grep` do?
`grep` searches text and prints lines that match a specified pattern. In this task, `grep "ERROR" sys_events.log` displays error events, while `grep -E "root|user01" sys_events.log` matches lines containing either `root` or `user01`.

## What does `chown` do?
`chown` changes the owner and/or group associated with a file or directory. `sudo chown root:root confidential.txt` makes `root` the file owner and sets its group to `root`.

## What does `usermod` do?
`usermod` modifies an existing user account. The `-aG sudo` options append the user to the `sudo` supplementary group without replacing the user's other supplementary group memberships.

## What happened after ownership changed?
With permissions set to `600`, only the file owner has read and write access. After `sudo chown root:root confidential.txt`, `root` owns the file, so `forensic01` should be unable to read or modify it as an ordinary user. The expected “Permission denied” result demonstrates that ownership and permission bits work together to control access.
