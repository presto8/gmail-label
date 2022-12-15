# gmail-label

## Overview

gmail-label is a tool to query and modify Gmail labels using IMAP.

## Example

    gmail-label msgid:"xxxx" --query  ->  "Junk"
    gmail-label msgid:"xxxx" --add "Misc " -> "Junk Misc"
    gmail-label msgid:"xxxx" --remove "Junk"  -> "Misc"

# Theory of operation

- User credentials stored in .config file
- All data is cached locally in a CSV file
- On first run, retrieve:
  - All Message-IDs and labels from imap.gmail.com.
  - UIDVALIDITY and HIGHESTMODSEQ for "[Gmail]/All Mail" folder
- On subsequent runs, if UIDVALIDITY has not changed, then only retrieve changes
- If UIDVALIDITY has changed, then delete the local cache and rebuild from scratch.


# Notes

>>> gmail.cmd('status', 'Misc', '(uidvalidity highestmodseq)')
OK [b'"Misc" (HIGHESTMODSEQ 16407774 UIDVALIDITY 27)']

>>> x = gmail.cmd('fetch', '1:*', '(X-GM-LABELS) (CHANGEDSINCE 16407774)')
OK [b'52352 (X-GM-LABELS (Misc) MODSEQ (16407783))']

- build cache of gmail tags store in shelve
  - imap status(uidvalidity uidnext)
    - if new uidvalidity: rebuild cache
    - else: just get new messages

- imap
  select All Mail
  fetch 1:N for all messages to get x-gm-labels
  now we have all the gmail labels and uids


# Recipes

FETCH 1:n (X-GM-LABELS) -> fetch labels of all messages
Pretty fast -> can do 50,000 messages in a few seconds


# References

## How to use gmail imap extensions
https://developers.google.com/gmail/imap/imap-extensions

## UIDVALIDITY
https://dev.to/kehers/imap-new-messages-since-last-check-44gm
