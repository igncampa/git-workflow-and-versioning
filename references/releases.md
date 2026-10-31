# Releases and Consumer Compatibility

Read this file for these tasks: choose a software version, change a contract or changelog that consumers use, or prepare a release. The approval, integration, and retention gates of the Git skill continue to apply. Usual commits, pipeline runs, and trial archive tags are not releases.

## Scope and Authority

Obey the existing release process of the repository and its authoritative version source. This source can be a tag, a manifest, or a release tool. Do not add a different version mechanism during a change that is not related. Do not manually edit competing copies of the version. Make sure that the built artifact, the version metadata, the release tag, and the changelog agree.

If the project has a changelog for consumers, update it with the related change. Usually, put the entry under Unreleased until the release. Make release tools only when the project needs them or when the user asks for a release. Do not change each development increment into a release. Publish packages, releases, and release tags only through the approved workflow.

## Semantic Versioning

For software that has consumers, use the version policy that the project uses. When the project uses Semantic Versioning, use this table:

| Bump | Meaning |
|---|---|
| MAJOR | The change breaks a supported consumer contract. Consumers must change their use. |
| MINOR | The change adds functions that are compatible with earlier versions. |
| PATCH | The change repairs a defect. It does not break the supported contract. |

Decide from the compatibility that consumers can see. Do not decide from the size of the diff. Not all changed outputs are breaking software changes. New input data, selected run parameters, and repairs that bring back the supported contract are different from a removal or a change of that contract. Do not hide a real breaking change in a patch. If you are not sure, examine the problem. If a compatibility risk stays, tell the user about it. Do not guess that it is safe.

Breaking changes need a migration note. When possible, they also need a deprecation period. Find the consumers and defaults that the change affects. Do not look only at renamed symbols.

## Immutable Release Identity

A release is an immutable point in history. It is not a branch that moves. Use the tag convention of the project. Usually this is an annotated tag, for example `v1.4.0`, on the exact integrated release commit. Some builds get the version from the tag. For these builds, you can make the local tag before the build. Do not publish until you verify the artifact and its version. Never move an existing published version or tag to hide a correction. Make a new version.

Use the existing release tools to keep the version sources the same. If the project gets versions from Git, keep that model. If the project uses a manifest, make sure that the tag agrees with the manifest. When publication is necessary, make sure that the approved upstream has the intended commit and tag.

## Changelog

A changelog is a selected list for consumers. It is not a copy of all Git history. Put entries in these groups when applicable: `Added`, `Changed`, `Fixed`, `Deprecated`, `Removed`, and `Security`. Put the newest release first. Tell the effect on users and the effect on migration. Do not show empty groups. Do not include internal refactoring that is not important to consumers.

Write the entry at the same time as the change, while you know its effect. At the release, put the collected entries under the actual version and date. Add links to existing migration documentation. Do not copy that documentation.

## Release Gate

- The version bump agrees with the supported compatibility contract.
- The release artifact, the authoritative version metadata, the immutable tag, and the changelog agree.
- The related release validation and migration notes are complete.
- Publication obeys the configured, approved process. A local tag only is not a published release.
