#### Creating a FQDN
```
192.168.56.10 192.168.56.10 mero.devops.local mero
```

#### Example using `curl` to send email via SMTP (smtps://smtp.gmail.com:465)

```
curl --url 'smtps://smtp.gmail.com:465' --ssl-verifyhost 2 --ssl-verifypeer 1 \
--mail-from 'a.a@gmail.com' --mail-rcpt 'a.a@gmail.com' \
--upload-file mail.txt --user 'a.a@gmail.com:vrep cfqq tmnu zhcr' --insecure
```

#### For testing: Install `nmap-ncat`
```
sudo yum install nmap-ncat
```

#### Another example script for sending mail using `curl` with SMTP

```bash
#!/bin/bash
SERVER='smtp.gmail.com'
PORT='465'
USER='mero@gmail.com'
PASS='password'
SENDER_ADDRESS='usko@gmail.com'
SENDER_NAME='usko'
RECIPIENT_NAME='usko'
RECIPIENT_ADDRESS='usko@gmail.com'
SUBJECT='Scripted Mail'
MESSAGE=$'This is the first line\nThis is the second line'
ATTACHMENT_FILE='/home/anon/m.xlsx'
ATTACHMENT_TYPE="$(file --mime-type "$ATTACHMENT_FILE" | sed 's/.*: //')"
curl --ssl-reqd --url "smtps://$SERVER:$PORT" \
    --user "$USER:$PASS" \
    --mail-from "$SENDER_ADDRESS" \
    --mail-rcpt "$RECIPIENT_ADDRESS" \
    --header "Subject: $SUBJECT" \
    --header "From: $SENDER_NAME <$SENDER_ADDRESS>" \
    --header "To: $RECIPIENT_NAME <$RECIPIENT_ADDRESS>" \
    --form '=(;type=multipart/mixed' \
    --form "=$MESSAGE;type=text/plain" \
    --form "file=@$ATTACHMENT_FILE;type=$ATTACHMENT_TYPE;encoder=base64" \
    --form '=)'
```

### Explanation:
- Replace placeholders like `mero@gmail.com`, `password`, `usko@gmail.com`, etc., with your actual credentials.
- Adjust paths and filenames (`mail.txt`, `m.xlsx`) according to your setup.
- Ensure proper security measures (`--ssl-verifyhost`, `--ssl-verifypeer`) based on your environment's requirements.

These notes should help you quickly refer to and understand how to send emails using `curl` and SMTP in a markdown-friendly format.