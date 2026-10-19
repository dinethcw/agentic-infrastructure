# Agentic Infrastructure

A Quarto research notebook for Dineth's applied AI investigation.

## Current setup status

The source scaffold has passed a local Quarto 1.10.19 render. GitHub access is connected. Pages activation and the first live deployment remain to be verified.

Repository: dinethcw/agentic-infrastructure
Site target: https://dinethcw.github.io/agentic-infrastructure/

## One-time setup

1. Create a public GitHub repository named agentic-infrastructure, initialized with a README so it has a main branch. Do not add confidential notes.
2. Grant the ChatGPT GitHub connection access to that repository.
3. Ask the assistant to populate the repository with this scaffold.
4. In repository Settings > Pages > Build and deployment > Source, select GitHub Actions.
5. Verify the Build and publish notebook workflow succeeds and the site opens.

GitHub connector access may not permit writes to workflow files. If that occurs, the assistant must provide the exact file and smallest required manual step, without claiming deployment is complete.

## Everyday workflow

Paste notes into the project conversation with the paper link where possible. The assistant formats them, checks source metadata, preserves meaning, and identifies unresolved questions. You review substantive interpretations before public publication.

Use "Publish these notes" when they are ready. The assistant commits the approved material; GitHub Actions renders and deploys it. Automatic deployment begins at a GitHub push, not merely when a message is sent to ChatGPT.

For drafts kept off GitHub, this setup needs a separate private storage location. A public repository's draft files remain publicly accessible.

## Local preview

Install Quarto 1.10.19, then run:

```sh
quarto preview
```

Build with `quarto render`. No Python/R execution, API key, or model calls are required for the initial notebook.

## Structure

- notes/: published reading entries
- experiments/: experiment write-ups
- records/: public-approved original notes and supporting evidence
- templates/: reading and experiment guides (not rendered)
- progress.qmd: meaningful decisions and next actions
- AGENTS.md: durable assistant workflow instructions

## References

- https://quarto.org/docs/publishing/github-pages.html
- https://docs.github.com/en/pages/getting-started-with-github-pages/using-custom-workflows-with-github-pages
