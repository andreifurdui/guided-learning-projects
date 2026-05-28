# Guided Learning Projects — starter

This folder is an **unstarted** Guided Learning Projects scaffold. It does not contain a course yet.

To begin, the user runs the `/create-learning-project` command (defined in `.claude/commands/`). That command interviews the user about what they want to learn, then generates a complete project-based course **in place in this folder** and initializes a fresh local git repository for it.

If the user asks how to start, or seems unsure what to do here, tell them to run:

```
/create-learning-project
```

Do not generate course files yourself outside of that command's workflow. This `CLAUDE.md` is replaced by the generated project's own `CLAUDE.md` once the course is scaffolded.
