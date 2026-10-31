# Releases and Consumer Compatibility

Read when choosing a software version, changing a consumer-facing contract/changelog, or preparing a release. The Git skill's approval, integration, and retention gates still apply. Ordinary commits, pipeline runs, and trial archive tags are not releases.

## Scope and Authority

Follow the repository's existing release process and authoritative version source, whether a tag, manifest, or release tool. Do not retrofit a different versioning mechanism during an unrelated change or hand-edit competing version copies. Keep the built artifact, version metadata, release tag, and changelog consistent.

Update an existing consumer-facing changelog alongside the relevant change, usually under Unreleased until release. Create release machinery only when required by the project or requested release, not to turn every development increment into a release. Publish packages, releases, and release tags only through the authorized workflow.

## Semantic Versioning

For software with consumers, use the established version policy; when Semantic Versioning applies:

| Bump | Meaning |
|---|---|
| MAJOR | Breaks a supported consumer contract; consumers must adapt |
| MINOR | Adds backward-compatible functionality |
| PATCH | Fixes a defect without breaking the supported contract |

Judge observable compatibility, not diff size. Not every changed output is a breaking software change: new input data, selected run parameters, and fixes that restore the supported contract are different from removing or changing that contract. Do not hide a genuine breaking change under a patch. Investigate uncertainty and flag unresolved compatibility risk rather than guessing that it is safe.

Breaking changes need a migration note and, where practical, a deprecation window. Identify affected consumers and defaults, not just renamed symbols.

## Immutable Release Identity

A release is an immutable point in history, not a moving branch. Use the project's tagging convention, typically an annotated tag such as `v1.4.0`, attached to the exact integrated release commit. Tag-derived builds may require creating the local tag before building; publication waits until the artifact and its version are verified. Never move an existing published version/tag to disguise a correction; issue a new version.

Use existing release tooling to keep version sources consistent. If the project derives versions from Git, retain that model; if it uses a manifest, keep the tag aligned with it. Confirm the authorized upstream contains the intended commit and tag when publication is required.

## Changelog

A changelog is curated for consumers, not a dump of Git history. Group entries under `Added`, `Changed`, `Fixed`, `Deprecated`, `Removed`, and `Security` as applicable, newest release first. Describe user impact and migration implications; omit empty categories and internal refactoring noise.

Write the entry with the change while its impact is known. At release, associate the accumulated entries with the actual version and date. Link existing migration documentation rather than duplicating it.

## Release Gate

- The bump matches the supported compatibility contract.
- The release artifact, authoritative version metadata, immutable tag, and changelog agree.
- Relevant release validation and migration notes are complete.
- Publication follows the configured authorized process; a local tag alone is not a published release.
