# legacy writeup

## recon

# low-priv ssh (`sys4dmin:adminpass`), and a legacy note-taking app listening only on `127.0.0.1:8080`. goal is privesc to root.

# no sudo on the box (not even installed), but `ps aux` shows the app running as root:


# python2 + BaseHTTPServer, runnin as root. working dir /var/run/s3cr3t_py_d1r/ holds three root-owned files: webapp.py (the note app), backup.py (backup helper), 'backup.sh' (wrapper around backup.py)

## the webapp

# it's a markdown notes app, notes stored as json in /app/notes. two things matter.

# single-session lock: a global _active_token gives the first client a cookie, everyone else gets 403. so step zero is grabbing the session first

# the bug: /save validates that the body parses to a dict, but /upload writes the raw request body to /app/notes/id.json with no validation. we control the exact bytes of a note file that root's backup will later read.

## the backup chain

# cron runs the backup every 2 minutes as root, wrapped in timeout:

# exact line from cron :
## */2 * * * * root cd /tmp && /usr/bin/timeout --signal=QUIT --kill-after=5s 10s /var/run/s3cr3t_py_d1r/backup.sh ...

# after 10s timeout sends SIGQUIT to backup.sh, which traps it and runs gcore on the live python process, then leaves the core at 0644 - readable.

# meanwhile backup.py loads the root password from /root/.env into memory at the top of main() and holds it for the whole run, then does json.loads() on each note.

## the main idea

# the box is python2, where parsing a huge decimal string into an int/long is O(n²) with no digit cap. json.loads() over one giant integer hangs for a long time. so we exploit exactly that  .

# so i upload a note that's just millions of digits. that's the whole point
## exploitation

# grab the session cookie mdsession against 127.0.0.1:8080.

# so we write a ton of 9s (3 million) as an exploit:

# python -c "open('/tmp/payload','w').write('9'*3000000)"
# curl -s -b /tmp/.mc -X POST --data-binary @/tmp/payload http://127.0.0.1:8080/upload


# it gets a low hex id ("5d300476.json"), so it sorts and parses first → instant hang.

# wait for the next tick. at t+10s backup.log shows SIGQUIT caught and the dump written, and the core lands at 0644 and gives us the dump:


#  /tmp/memdump_314.core


# we grep the whole file ,no sudo yet, but /bin/su is SUID, so we easily escalate


# os.execvp("su", ["su", "-c", "id; cat /root/flag.txt", "root"])


# uid=0(root) gid=0(root) groups=0(root)


# flag drops out. gg