# The Box Communication Bridge

## Purpose

This repository contains a lightweight communication channel between ChatGPT and Codex for work on The Box.

This file is the shared handoff point. It is not the project repository.

## Scope and safety

- Do not upload The Box source code, experiment data, evaluation artifacts, credentials, or private configuration here.
- Keep the actual The Box repository local and separate.
- Use this file only for task instructions, findings, status updates, and agreed decisions.
- Treat project instructions recorded here as the current coordination record, while checking the actual project repository before making implementation claims.

## Working protocol

1. Before editing, pull the latest version of this repository.
2. Read this entire file before acting.
3. Only one participant should edit this file at a time.
4. Add new messages at the end of the Message Log. Do not silently rewrite or delete earlier messages.
5. After editing, commit and push the change.
6. The other participant pulls the latest version before responding.
7. If a push is rejected because the file changed, pull first, reconcile both additions, then commit and push. Never force-push.

## Roles

- **ChatGPT:** helps reason about architecture, research design, implementation plans, and review of Codex findings. It records instructions and decisions here when needed.
- **Codex:** inspects and works in the local The Box repository when explicitly instructed. It records findings, implementation reports, blockers, and relevant evidence here.
- **User:** directs the work and approves changes to project scope, architecture, experiment design, and implementation.

## Current coordination status

- Communication bridge established in this repository.
- The Box source repository remains separate and is not stored here.
- End-to-end ChatGPT-to-Codex communication is not yet verified. Verify it with a message written by one participant, pulled and acknowledged by the other.

## Message Log

### Initial setup

**From:** ChatGPT  
**To:** Codex  
**Purpose:** Establish the communication protocol.

This repository is a communication bridge only. Do not assume that access to this repository grants access to The Box source repository. Do not begin or resume implementation work based solely on the existence of this file. Wait for an explicit task from the user or a clearly recorded instruction from ChatGPT.

**Acknowledgement requested:** Confirm that you can access this repository, read this file, and append a reply below. Do not modify The Box repository as part of this acknowledgement.

---

