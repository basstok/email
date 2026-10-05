# Connect your email to Basstok

Keep your address. Read new mail, write emails and reply beside your messages in
[Basstok](https://basstok.com/), on Web, iPhone, iPad and Android.

This guide covers the supported email providers, protocols and setup choices.
**Email connections are in early access.** Implemented support is not a promise
that every provider is available or verified in the current release. Gmail and
Microsoft public connection setup and live acceptance remain outstanding.

## Choose the connection you need

| Your email | Basstok's approach | Sign-in or setup | Current boundary |
| --- | --- | --- | --- |
| Apple iCloud Mail | Encrypted IMAP for receiving; SMTP for sending | Apple app-specific password; incoming settings suggested automatically, outgoing settings entered separately | Requires your authorized mailbox and a delivery test |
| Gmail / Google Workspace | Gmail API, not Gmail IMAP | Google consent for reading and sending; only granted permissions apply | Implemented; provider configuration, public verification and live mailbox acceptance are not complete |
| Outlook.com / Microsoft 365 | Microsoft Graph, not password-based Outlook IMAP | Microsoft consent for reading and sending; only granted permissions apply | Implemented; provider configuration, applicable consent and live mailbox acceptance are not complete |
| Other compatible mailboxes | Encrypted IMAP for receiving; SMTP for sending | Provider-issued password or app password and the provider's server settings | Compatible password-based connections; no blanket compatibility claim |
| Amazon SES | Regional SMTP service for sending | SES SMTP credentials and a verified sending identity | Sending only; your region, sandbox, recipient and sending-limit requirements still apply |
| Mail forwarded from an existing address | Incoming SMTP to a Basstok Chat address | Enable a receiving address, then configure forwarding at your email provider | Does not require moving the domain or connecting mailbox credentials |
| An address on a domain you control | Incoming SMTP through your domain's MX records | Verify the exact address with a DNS TXT record, then deliberately change mail routing | Only connected destinations receive mail; no catch-all |
| Basstok account and notification email | Separate service delivery; optional Admin-selected notification SMTP | Account security and optional notifications keep their own delivery rules | Never used as a fallback for personal email |

An inbox connection receives new mail without importing the whole historical
mailbox or changing read/deleted state at your provider. Sending uses an
authorized Google/Microsoft connection or your explicit outgoing SMTP settings.
Receiving permission alone is not sending permission. Connecting email is not
Basstok login verification or Admin activation.

## Start with your inbox

1. Open **Messages → Connect email**, enter your email address and continue.
2. Follow the suggested connection. For an unfamiliar or custom-domain address,
   choose the actual provider if offered, or supply its encrypted IMAP settings.
   Basstok does not guess who hosts a custom mailbox.
3. Complete the provider's consent screen or enter the appropriate app password.
   If a provider connection is unavailable, use forwarding where supported;
   Basstok does not ask for a Google or Microsoft password as a workaround.
4. Wait for receiving setup to finish. Then send a new test email from another
   address and use **Open messages** to find it in the selected Chat.

Connect email starts a private Chat containing only you. You can also configure
receiving in a Chat you created and still participate in. An Admin cannot connect
or inspect another person's private Chat merely by being an Admin.

Mail received into a shared Chat is visible to its authorized participants.
Choose the destination carefully before connecting a personal mailbox. Receiving
an email never gives its sender membership or access to your Chat history.

## Apple iCloud Mail

Basstok recognizes `@icloud.com`, `@me.com` and `@mac.com` and suggests the
incoming iCloud settings:

| Setting | Value |
| --- | --- |
| Incoming server | `imap.mail.me.com` |
| Port | `993` |
| Security | TLS from the start of the connection |
| Username | The name before `@`; use the full address if your account requires it |
| Password | An Apple app-specific password, not your Apple Account password |

Create the password through your Apple Account and enter it only in Basstok's
authenticated connection form. This is the IMAP/app-password connection, not an
Apple sign-in integration. See [Apple's current iCloud Mail settings](https://support.apple.com/102525).

To send, open **Write an email → Outgoing server settings** and use:

| Setting | Value |
| --- | --- |
| Outgoing server | `smtp.mail.me.com` |
| Port | `587` |
| Connection security | `STARTTLS` |
| Username | Your full iCloud Mail email address |
| App password | The Apple app-specific password used for incoming mail |

Use an address Apple permits that account to send from. Receiving setup does not
automatically save these outgoing settings. These values follow
[Apple's mail-server instructions](https://support.apple.com/en-us/102525);
an actual send and receipt still establish whether your account works.

## Gmail and Google Workspace

Basstok uses the **Gmail API with Google OAuth**, rather than requesting full-mail
IMAP authorization. It requests `gmail.readonly` for receiving and `gmail.send`
for sending. A read-only grant keeps receiving available but does not enable
sending. Neither scope permits deleting mail or changing its read state. See
[Google's scope reference](https://developers.google.com/workspace/gmail/api/auth/scopes).

- Recognized Gmail addresses select Google. A custom Workspace address can choose
  Google when that connection is available.
- Consent is through Google, never a Google password collected by Basstok.
- Basstok receives new INBOX mail after the connection's starting point. It does
  not download the old mailbox as part of setup.
- Disconnecting a Chat stops its receiving. Provider-wide authorization removal
  is a separate action in your Google Account and can affect other connections.

**Not yet generally available:** Basstok's Google application configuration,
required public verification and authorized live-mailbox acceptance must finish
before this is advertised as a ready connection. Google classifies this read-only
scope as restricted; public distribution has additional requirements. An
implemented connection or a successful development check is not Google approval.

## Outlook.com and Microsoft 365

Basstok uses **Microsoft Graph with Microsoft OAuth**. It requests delegated
`Mail.Read`, `Mail.Send`, `User.Read` and offline access, not application-wide
mailbox access. Sending becomes available only when `Mail.Send` was actually
granted; a receiving connection alone is insufficient. Microsoft's
[permission reference](https://learn.microsoft.com/en-us/graph/permissions-reference#mailread)
describes these permissions.

- Outlook, Hotmail, Live and MSN addresses select Microsoft. A custom Microsoft
  365 address can choose it when configured.
- Consent is through Microsoft, not a Microsoft password entered in Basstok.
- Setup prepares the INBOX before new-mail receiving starts; preparation is not
  a download of historical mail bodies.
- Personal, work and school account availability depends on the configured
  application and account policies. An organization's administrator may need to
  approve consent. Shared and delegated mailboxes are not supported by this path.

**Not yet generally available:** provider application setup, applicable consent
and live mailbox acceptance remain outstanding. A saved connection or completed
browser redirect alone is not proof that mail is arriving.

## Other providers: encrypted IMAP

Use your provider's documented incoming hostname, TLS port, username and password
or app password. The usual implicit-TLS port is `993`; use the provider's actual
settings. The connection must use a trusted certificate matching its hostname.

Basstok reads **new INBOX messages only**, without marking them read or deleting
them at the source. It does not synchronize folders, flags or deletions, and does
not provide generic IMAP OAuth, POP3, local desktop bridges, plaintext IMAP or an
IMAP STARTTLS upgrade. OAuth-only providers need an explicitly supported provider
connection rather than a password workaround.

Receiving checks run periodically, with an explicit check action available in
settings. Provider push subscriptions and IMAP IDLE are not part of the current
connection; there is no instant-delivery guarantee. If mailbox identity or access
changes, setup may require attention rather than silently reimporting old mail.

IMAP does not send email. Use the same provider's documented outgoing SMTP
settings separately; receiving credentials are not automatically reused for
submission. Basstok keeps its sent record in the Chat, without using IMAP to
append a copy to a remote Sent folder.

## Forward an existing address

This is often the simplest choice when you want to keep your current provider
and cannot or do not want to connect it directly.

1. In the Chat's email settings, open **More options** and enable its receiving
   address, where incoming email is available.
2. Copy the address Basstok displays. Do not invent an address from a Chat title.
3. Set up forwarding at your existing provider and complete its verification
   process if required. Choose whether that provider should retain a copy.
4. Send a new email through the original address and confirm it arrives in the
   intended Basstok Chat, including any attachment you need.

Forwarding does not import old mail or authorize replies from the original
address. Disabling and re-enabling a generated receiving address creates a new
one; update your forwarding rule if you do that. Existing received history stays.

## Receive directly on your own domain

If you control the domain, **Use an existing email address** connects an exact
address to a Chat without requiring a full mailbox connection.

1. Enter the existing address you want to receive, such as `inbox@example.com`.
2. Publish the exact TXT proof shown by Basstok and complete its verification.
3. Review all existing mail destinations on that domain before changing MX.
4. When ready, use the SMTP hostname and MX priority shown in Basstok, then send
   a real external test to every required destination.

**MX changes affect the whole domain, not just one address.** Basstok rejects
unconnected recipients; it does not provide a catch-all, infer aliases or strip
plus tags. Do not redirect a domain that still depends on other mailboxes without
planning their delivery too. Forwarding is safer when you only want one mailbox.

Changing MX does not move your website or authorize sending mail from the domain.
A verified address is not proof of public delivery. Restore your previous mail
routing before disconnecting if other mail should return to the old provider.

## Write an email or reply

1. In a Chat you created, choose **Write an email**, or **Reply by email** on an
   incoming email. Review the recipient and subject, then write your message.
2. A connected Google/Microsoft mailbox with sending permission supplies the
   sender. Otherwise choose **Connect email** or **Outgoing server settings**.
   Your open draft stays in place while you connect.
3. If using SMTP, enter the address, server and credentials supplied by your
   provider, then **Save sending connection**. Explicit SMTP takes precedence
   over a connected mailbox; inspect the selected sender before sending.
4. Choose **Send email**. The resulting email appears in that Chat with its
   recipient, subject, text and sending status.

Only the Chat's current human creator can connect sending or send through it.
Admin status alone does not grant access, and other participants cannot use the
creator's credentials. They can read sent messages within their authorized Chat
history. Incoming sender addresses are not authenticated identities: check the
reply recipient before sending sensitive information.

### Understand the result

- **Accepted by your email provider:** the service accepted the submission.
  It does not prove delivery, inbox placement or that the recipient read it.
- **Email was not sent:** the attempt was rejected. Fix the reported problem
  before choosing to send a new attempt.
- **Sending not confirmed:** it may already have been sent. Use **Check send
  result** to check the same attempt, not send another copy. Check your provider's
  sent mail or other delivery records where available before starting over.

An uncertain request keeps its original recipient, text and identity while you
check it. Disconnecting the sender does not make that check resend. There are
no automatic resends; starting a new attempt after an unknown result can create
a duplicate.

Drafts are local, not synchronized between devices. Native apps retain the open
draft in memory and ask before discarding it; closing the app can lose it. Web
keeps bounded drafts in the current tab for up to 24 hours and clears them on
sign-out. Sent history, unlike an unfinished draft, remains in the Chat.

Current sending supports **one recipient, plain text up to 16 KiB, and a subject
up to 512 UTF-8 bytes**. Outgoing attachments, CC/BCC and scheduling are not yet
available. Receiving attachments is a separate capability.

## Outgoing SMTP and Amazon SES

Supply the exact sender address, public SMTP hostname, port, credentials and
security mode. Supported modes are implicit TLS or mandatory STARTTLS before
authentication; supported credential mechanisms are SMTP AUTH PLAIN and LOGIN
over that encrypted connection. A provider requiring SMTP OAuth alone does not
fit this credential-based setup. Do not disable TLS to make it connect.

Saving the connection authenticates and checks the sender, but **does not submit
a test email**. It does not prove recipient delivery, spam-folder placement or
sender-domain approval. Follow your SMTP provider's setup instructions:

- **SPF:** authorize the actual sending service without discarding existing
  authorized senders or adding a second SPF policy for the same name.
- **DKIM:** use the provider's exact TXT/CNAME records and selector values.
- **DMARC:** review the domain's existing policy and alignment; do not blindly
  replace it while connecting another sender.

For **Amazon SES**, select the region's SMTP endpoint and use credentials created
for the SES SMTP interface, not an AWS console password or a general API key.
Your sending identity must be verified in that region. In the SES sandbox,
recipients also need verification unless you use its mailbox simulator; moving
out of the sandbox and any applicable quotas remain your account's responsibility.
See [SES SMTP setup](https://docs.aws.amazon.com/ses/latest/dg/send-email-smtp.html)
and [sandbox restrictions](https://docs.aws.amazon.com/ses/latest/dg/request-production-access.html).
SES provides outgoing service here, not a connected inbox.

Verify actual delivery before relying on any new sender. A successful SMTP login
is not a delivery test, and an address's MX records do not authorize sending.

## Account, security and notification email

Basstok's required account, recovery and security mail uses its separate service
delivery. An Admin can choose a custom SMTP sender for eligible notification
delivery, with sender-domain DNS checks beside that administrative setup.
That setting is not personal sending, never overrides the security transport,
and is not a fallback when your personal connection is missing.

Optional email preferences remain independent of required security/account email.
Received external email does not trigger another optional email alert, avoiding
forwarding loops. In-app and configured native notifications still apply.

## Attachments, privacy and current limits

- Received bodies are presented safely; external email is not allowed to load
  remote tracking resources or run active HTML. Original HTML layout is not a
  rendering promise. Inline MIME parts can appear as ordinary attachments.
- Current receiving limits are **1 MiB per complete email**, including encoded
  attachments and headers, and up to **32 attachments**. Encoding overhead counts;
  a 1 MiB file does not fit inside a 1 MiB email. Message bodies also have limits.
- Oversized, malformed or unsupported messages can be rejected or stop mailbox
  receiving with an error. A connected status does not override those limits.
- Sender names and addresses are external claims, not authenticated Basstok
  identities. Email does not grant access to earlier private Chat history.
- Disconnecting stops future receiving, not the history already received.
  Provider authorization and passwords can be revoked separately at the provider.
- Keep passwords, OAuth codes, tokens and private mail out of GitHub issues and
  screenshots. For help, share only the connection method and non-secret status
  with [Basstok support](mailto:mail@basstok.com).

## API and integration

The [Basstok API](https://github.com/basstok/api) publishes the current request,
response and authorization contracts. Key surfaces include:

| Purpose | API path |
| --- | --- |
| Address-first connection assistance | `/api/v1/chats/{chatId}/mailbox/setup` |
| Mailbox status, selection and disconnect | `/api/v1/chats/{chatId}/mailbox` |
| Check for new mail | `/api/v1/chats/{chatId}/mailbox/check` |
| Google consent | `/api/v1/chats/{chatId}/mailbox/google` |
| Microsoft consent | `/api/v1/chats/{chatId}/mailbox/microsoft` |
| Generated receiving address | `/api/v1/chats/{chatId}/incoming-email` |
| Exact custom-domain receiving address | `/api/v1/chats/{chatId}/incoming-email/address` |
| Personal sending status and SMTP selection | `/api/v1/chats/{chatId}/email/sending` |
| Explicit personal send or reply | `/api/v1/chats/{chatId}/email/send` |
| Admin notification SMTP sender | `/api/v1/organization/email` |
| Sender-domain DNS checks | `/api/v1/organization/email/dns-check` |

Personal receiving/sending setup and personal sends require the current human
Chat creator and participation. Only the separate Organization notification SMTP
setup requires Admin authority. Connecting a mailbox neither authenticates a
Basstok session nor activates administration. These personal operations do not
accept delegated App authority; an App cannot use mailbox consent to bypass its
scopes or Chat access.

The send operation requires an `Idempotency-Key` for each explicit attempt.
Keep its exact connection, recipient, subject, body and optional reply reference
when checking an uncertain result. Reusing that key with different input fails;
an exact repeat returns the saved result rather than resending. Use the published
contract for field names, limits, errors and session requirements.

This repository documents external email connections and user-visible behavior.
It does not publish deployment credentials, private implementation or an
unsupported promise of compatibility with every mail service.
