# HomeLabDarcy

A home server I'm building on an old laptop to learn Linux, networking, cloud and cybersecurity. I'm recording every step in the [build log](doc/build-log.md), including what goes wrong and how I fix it.

## How I use AI

I use Claude (an AI assistant) as a tutor and assistant on this project, with clear rules so I stay in control and actually learn:

- **I do the work and make the decisions.** Claude explains things I don't know and helps plan the steps. I run every command myself and decide what goes on the server.
- **I understand before I move on.** Claude explains each step in plain English, and I ask questions until it makes sense. Everything I learn goes into my [learning notes](doc/learning-notes.md), which Claude can quiz me on.
- **Nothing changes without my approval.** Claude never edits this repo directly. It proposes changes as pull requests, and I review and merge them myself.
- **Automation is limited.** A daily scheduled task checks my learning notes and project instructions for changes and opens a single pull request to keep this repo in sync. It batches each day's changes into one pull request to keep AI usage down, and it stops after two weeks.
- **Security comes first.** No passwords, keys or my public IP address ever go into this repo or into what I share with Claude.
- **It's all transparent.** The exact instructions I give Claude are in [claude-project-instructions.md](doc/claude-project-instructions.md).
