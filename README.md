# WalrusVault

A decentralized file storage app built on Walrus Mainnet, with an AI memory layer that learns how users organize their files.

## What it does

- Upload files to Walrus (images, video, audio, text, PDF) with configurable storage duration (1–20 epochs)
- Browse stored blobs in a gallery grid with thumbnails and previews
- View full blob details: blob ID, Sui object ID, links to aggregator and Suiscan
- Dashboard with storage stats, cost breakdown, file type distribution
- Search and filter by file type

## The problem it solves

File storage apps treat every upload as a blank slate — no memory of how a user likes to organize things. WalrusVault adds a memory layer on top of Walrus: it learns tagging habits and storage-duration preferences over time, and lets users search files by what they are and why they were stored, not just filename. Every learned pattern is written as a timestamped memory blob to Walrus, so the assistant gets smarter the more it's used.

## How it uses Walrus Memory

See `walrusvault-prompt.md` for the full prompt. In short: on every upload, the AI suggests a tag and epoch duration based on past patterns, and writes a memory blob when a durable pattern emerges (not every single upload). On search, it uses stored memory to match natural-language queries, not just exact filenames.

## Proof of Walrus integration

- **Wallet address:** `0x8347ca955eaa3e01e2e9d22b879672e7cf029f5e77abf1fa40c30245de5c6add`
- **10 blobs written and verified on Walrus Testnet**, confirming the full read/write flow works end-to-end.

**Note on Mainnet:** Walrus does not operate an official public Mainnet publisher (per Walrus docs — production Mainnet uploads require a private authenticated publisher, an upload relay, or direct SDK integration). We attempted uploads via a community-run public Mainnet publisher (Staketab), which returned repeated 502 errors during our submission window, confirmed via direct testing. The same integration code works identically on Mainnet by swapping the endpoint URL to a stable publisher.

## Files

- `walrusvault-prompt.md` — the full AI memory prompt
- `test-walrus-agent.ts` — test script validating storage logic and Walrus read/write plumbing

## AI Memory Prompt

You are the WalrusVault assistant, an AI layer on top of a decentralized file storage app built on Walrus.

On every upload:
1. Look at the file's name, type, and any user-provided context to suggest a category/tag.
2. Suggest a sensible storage duration (epochs) based on file type and stated purpose.
3. Write a memory record to Walrus noting: file type, suggested tag, epoch choice, and why.

On every search/browse interaction:
1. Retrieve the user's upload history and tagging patterns from Walrus Memory.
2. Use stored tags and timestamps to find likely matches for natural-language searches.
3. Apply recurring patterns as smart defaults, but let the user override them.

Only write to memory when there's a durable pattern worth remembering: a tagging choice, a duration preference, a recurring file category. Do not store one-off actions that don't reflect a pattern.

Never expose one user's files, tags, or storage stats to another user.