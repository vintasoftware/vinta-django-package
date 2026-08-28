# Changelog

All notable changes to this template are documented here. The format follows
[Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and this project adheres to
[Semantic Versioning](https://semver.org/spec/v2.0.0.html).

Versions are tagged, so `copier update` in a generated project pulls changes from the
newest tag rather than from the tip of `main`. Anything that changes the shape of a
generated project - a renamed question, a dropped file, a new answer that has no
default - is a breaking change and gets a major bump.


## [Unreleased]

### Fixed

- The `tox` Django matrix installed nothing. `uv-venv-lock-runner` builds the
  environment with `uv sync`, which is authoritative, so the `deps` factors were
  never applied and every environment tested whatever `uv.lock` pinned - a matrix
  that looked like it was varying Django while it was not. It pins through
  `commands_pre` now, and prints the version it resolved.
- The release workflow attached the signed artifacts to the release twice: the
  Sigstore action does that itself, and the hand-written `gh release upload` after
  it fails on the second copy - after the upload to PyPI, so the package is out
  and the release is red. It signs through `release-signing-artifacts` instead.
- The Sigstore action was pinned to a version from before Sigstore rotated its TUF
  root, which fails with `root was signed by 0/3 keys`.
- `uv version --short` can emit colour, which would never equal the tag it is
  compared against, and the release build ran for a `workflow_dispatch` from a
  branch whose name is not a version.

### Added

- Initial release of the template. Generates a reusable Django application packaged
  for PyPI with hatchling, uv, ruff, mypy in strict mode with django-stubs,
  pytest-django, tox and GitHub Actions across the supported Python and Django matrix,
  pre-commit, and a trusted-publishing release workflow.
- Questions covering project identity, authorship, license (MIT, BSD 3-Clause, or
  proprietary), and the floor of the support matrix.
- A derived support matrix: `python_support` in `copier.yml` maps each Django release
  to the interpreters it supports, so `tox.ini` and the CI matrix never emit an
  unsupported pair.
- `.copier-answers.yml` in generated projects, so `copier update` can replay the
  answers.
- A post-generation task that runs `uv lock`, so the lockfile the CI workflow and the
  pre-commit hooks expect is there from the first commit.
- CI for the template itself: five answer combinations are generated on every push and
  each one runs the full check suite it ships with.


[Unreleased]: https://github.com/vintasoftware/vinta-django-package/commits/main
