# Fallroom

**Live:** https://sjgant80-hub.github.io/fallroom/

A serverless, end-to-end encrypted peer-to-peer war-room for a private build cell. No server, no accounts, no telemetry, no build step — one HTML file that runs entirely in your browser.

## The sovereign way it connects

Two jobs, kept separate:

1. **Signaling** — you hand-carry one blob (invite → reply) to each other over a channel you already trust (Signal, in person). No signaling server exists.
2. **Connectivity** — a **STUN** server is asked once for your public address, which drops into that blob so a peer on another network can reach you **directly, peer-to-peer**. STUN never relays a message; it's a one-shot address lookup and then it's out of the loop. No TURN relay, no backend, no overlay.

> The blob carries your **public IP** — send it over Signal, not a public chat. It does **not** carry your room key or any message (those come from the shared passphrase and never leave your device), so whoever carries the blob still can't read the room.

## What protects a message

- **Ed25519** identity, generated in-memory, never leaves the device (ECDSA P-256 fallback).
- **PBKDF2 → AES-256-GCM** room key derived from a shared passphrase; a 3-word fingerprint lets you confirm out loud that you all derived the same key.
- **Signed + sealed** envelopes — every message is signed, and a small **WebAssembly proof-of-work seal** binds it to the room as an anti-spam toll.
- **DTLS-SRTP** transport underneath (WebRTC's own encryption).
- **Ephemeral** — nothing is stored; **Panic wipe** zeroizes keys and reloads.

## The fourth pillar — behavioral entropy

Identity, authorization and audit all still pass the moment a key is stolen. Fallroom adds a fourth check: **"are you behaving like yourself?"** It measures each person's keystroke-timing and message-cadence entropy, builds their baseline online (no training), and **flags** a message whose rhythm diverges from theirs — even when the Ed25519 signature, room key and proof-of-work seal are all valid. It also catches a scripted bot (too-regular timing) and a pasted/replayed message. It flags to the humans; it never blocks a cryptographically valid message.

This implements **Behavioral Entropy Signatures** from *"GEP-Based Security: Behavioral Entropy Signatures for Cybersecurity and DevOps"* — **Gary W. Floyd, Lumiea Systems Research Division · ThunderStruck Service LLC**. Fallroom measures only the behavioural domain a browser can see (keystroke + cadence), not the network or authorization domains in the paper; the signature is bound into each message's own signature so no relay can forge it.

Open the page and hit **Run self-test** to watch the whole crypto + BES path verify itself in front of you.

## Use it

Save `index.html`, open it (works off disk in Chrome/Edge, or from this link). Everyone: same **room name + passphrase** → *Derive room key* → confirm the three gold words match → trade the connect-blob over Signal → linked. It relays, so a chain of connections still delivers to everyone.

Part of the **FallForge** estate — p2p relay after `meshos`, crypto after `sovereign-demo`, the proof-of-work toll after `the-toll`. Konomi.

<sub>This is a private tool; it ships `noindex` and asks not to be crawled.</sub>
