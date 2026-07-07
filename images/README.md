# Drop your photos here

This is the local **drop zone** for the 3 photos you want analyzed.

- Everything in this folder is **git-ignored** (except this README), so your photos are
  **never committed, pushed, or uploaded** by the workflow. See the repo's `.gitignore`.
- Best mix: full-body **front**, full-body **side**, and a **back/¾** or clear **face** shot.
- Then run the analysis (e.g. `/style-analysis`, or "run the style analysis on the images
  in ./images"). Pointing at files on disk also enables the stronger vision path (the
  Analyst reads the files as a subagent).

## Privacy

Images are handled **locally only** — analyzed in the moment, never written to remote
storage, sent to any third-party service, embedded in artifacts, or committed to git.
Treat them as ephemeral; delete them when you're done.

> Honest caveat: if you run this through Claude Code / the Claude API, the vision step
> necessarily sends the image to the Claude model endpoint for inference — that's the
> model *seeing* the photo, the same as any use of the model. The workflow itself adds
> **no** further upload, storage, or transmission. If you need zero network transmission,
> you'd have to swap in a fully local vision model.
