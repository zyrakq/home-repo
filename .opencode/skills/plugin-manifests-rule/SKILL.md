---
name: plugin-manifests-rule
description: Rules for plugins.csv manifests. Use when adding, removing or changing plugins in the root plugins.csv or in any collection's plugins.csv.
---

# Plugin manifests

- Every collection's `plugins.csv` lists ALL plugins that collection needs, so
  someone who clones only the collection gets them all.
- The root `plugins.csv` lists ALL plugins of every collection.
- Duplication between the manifests is intentional. When adding a plugin to a
  collection, add it to the root `plugins.csv` as well.
