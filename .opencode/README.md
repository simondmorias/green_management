This folder is mapped a virtual folder to all other repos (then gitignored)

Recommend mapping to your root folder where you cloned the repos to:

```bash
ln -s ../management/.opencode ./.opencode
ln -s ./management/ROOT_AGENTS.md ./AGENTS.md
```

docs is also symlinked back this way so all repos have access to all documents