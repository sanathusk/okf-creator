Why OKF?
OKF represents catalog knowledge as plain markdown files with YAML frontmatter, organized in a directory hierarchy. That choice unlocks a few properties that are hard to get from a service-owned metadata store:

Human- and agent-readable. No SDK or query language stands between a reader and the content. An engineer can cat a concept; an LLM can ingest it verbatim into context.
Version-controllable out of the box. Bundles live in git. Pull requests, line-by-line diffs, blame, and review workflows just work — knowledge curation becomes a normal software-engineering activity.
Portable and lock-in free. A bundle is a directory. Ship it as a tarball, host it in any repo, mount it from any filesystem, or sync it to any system that speaks files. No proprietary API stands between you and your metadata.
Mixes structured and unstructured data deliberately. Use frontmatter for the few fields you want to query, filter, or index on (type, resource, tags, timestamp); use the markdown body for the prose, schemas, and example queries that LLMs and humans actually read.
Minimally opinionated, freely extensible. A small set of required keys ensures interoperability, but bundles can carry arbitrary extra frontmatter keys and arbitrary body sections without breaking consumers.
Composes with existing tooling. Many knowledge tools — Notion, Obsidian, MkDocs, Hugo, Jekyll — already speak markdown plus YAML frontmatter, so bundles can be browsed, edited, or rendered without custom UI.
Progressive disclosure built in. Auto-generated index.md files let an agent or human navigate the hierarchy one level at a time instead of loading the entire bundle into context.
Graph-shaped, not just tree-shaped. Concepts link to each other via normal markdown links, expressing relationships richer than the parent/child implied by the directory layout.
The net effect is that enrichment agents, consumption agents, and humans collaborate on the same artifacts in the same way they already collaborate on source code.
