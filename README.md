# shaclick — archived learning artifact (2020)

> **Archived. Do not use.** This is an old project I built in 2020 while
> learning how web-based phishing and browser APIs work. I'm keeping it public
> as a record of that learning and to explain the technique **defensively** —
> not as a working tool. It no longer functions (see below), and I would not
> deploy it against anyone. Secretly capturing a person's camera or location
> is a privacy violation and, in most countries, a crime.

## What it was

A small proof-of-concept that demonstrated a classic **reverse-tunnel phishing**
pattern: run a tiny web server on a laptop, expose it to the public internet
through a tunneling service, and hand out the resulting link. When someone
opened the link, the page recorded their IP address and browser, and attempted
to grab a still frame from their webcam.

I wrote it to understand three things that are genuinely worth understanding —
so this README explains the *concepts*, with no setup or usage instructions.

## The three ideas it taught me

**1. Reverse tunnels expose a local machine to the whole internet.**
Services like ngrok and Serveo open an outbound SSH/HTTPS connection from your
machine to their server, then forward public traffic back down that connection
to a port on `localhost`. That's incredibly useful for legitimately previewing
a local dev server — and it's the same mechanism that lets an attacker put a
laptop-hosted phishing page on a public URL without any hosting or DNS. The
lesson: a public link telling you nothing about who really hosts it or where it
runs.

**2. A browser will not open your camera without a visible permission prompt.**
This is the important defensive point. The web camera API (`getUserMedia`)
**cannot** silently access a webcam. The browser always shows a permission
dialog and a recording indicator; the "without their knowledge" framing these
tools advertise relies entirely on **social engineering** — convincing the
person to click *Allow* on a page that gives them a plausible reason to
(a fake video call, a "verify you're human" camera check, etc.). The camera
capture isn't a technical exploit; it's a trick played on the user. Remove the
click, and it does nothing.

**3. Metadata leaks with no permission at all.**
The IP address, browser, operating system and user-agent are sent to *any*
server you connect to, for every request, with no prompt — that's just how HTTP
works. Opening an unknown link already tells the other end roughly where you are
and what you're using.

## Why it no longer runs

The tunneling it depended on is dead: the hard-coded `ngrok.io` domains and the
old `bin.equinox.io` v1/v2 download URLs were retired years ago when ngrok moved
to v3 and new domains. I have deliberately **not** updated them. A broken
dependency is a fine place for a tool like this to stay.

## How to defend against this class of attack

- **Treat unexpected links as untrusted**, especially short-lived tunnel domains
  (`*.ngrok-free.app`, `*.serveo.net`, `*.trycloudflare.com`) arriving over chat.
- **Never click *Allow*** on a camera or microphone prompt you didn't initiate
  yourself on a site you trust. The prompt is your last and best line of defense
  — the whole attack depends on you saying yes.
- **Watch for the recording indicator** — the browser tab and your OS show when a
  camera or mic is live. If it lights up on a page that shouldn't need it, close
  the tab.
- **Assume metadata leaks.** Opening any link reveals your IP and device to that
  server. A VPN changes the IP it sees; it doesn't make you anonymous.
- **On the server side**, if you run reverse tunnels for legitimate dev work,
  don't leave them open, and don't forward anything you wouldn't put on the
  public internet.

## Status

Archived, 2020. Kept for reference and as a defensive write-up. Not maintained,
not functional, and not to be pointed at anyone.

— Sharqan Ahamed (Sha the IT Guy)
