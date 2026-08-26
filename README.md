# vinta-django-package

A [Copier](https://copier.readthedocs.io/) template for reusable Django applications
packaged for PyPI, with the tooling Vinta ships on every library already wired up.

## Usage

```bash
uvx copier copy --trust gh:vintasoftware/vinta-django-package my-new-package
```

Copier asks a handful of questions, writes the project, and runs `uv lock` so the
repository is immediately installable. Then:

```bash
cd my-new-package
git init && git branch -M main
uv sync --all-groups
uv run pre-commit install --install-hooks --hook-type commit-msg
uv run pytest
```

`--trust` is needed because the template runs `uv lock` as a post-generation task.
Drop it and run `uv lock` yourself if you would rather not.

## What you get

- **Packaging** — hatchling, PEP 621 metadata, `py.typed`, an sdist that carries the
  tests.
- **Dependencies** — uv with a `dev` dependency group and a committed lockfile.
- **Lint and format** — ruff, with the rule selection Vinta uses on Django projects.
- **Types** — mypy in strict mode with django-stubs, pointed at the test settings.
- **Tests** — pytest-django against an in-memory SQLite database, plus a `tests/testapp`
  to exercise the package from the outside the way a consumer would.
- **Matrix** — tox and a GitHub Actions matrix over every supported Python and Django
  pair, with Django's `main` branch reporting without gating the merge.
- **Hooks** — pre-commit, including a check for missing migrations and Conventional
  Commits on the commit message.
- **Release** — a GitHub Actions workflow that publishes to PyPI through trusted
  publishing (no stored API token) and attaches Sigstore signatures to the release.

## Questions

| Question | Default | Notes |
| --- | --- | --- |
| `project_name` | `Django Example App` | Human readable name |
| `distribution_name` | slug of the project name | What people `pip install` |
| `package_name` | slug with underscores | What people `import` |
| `django_app_label` | the package name | Must be unique across `INSTALLED_APPS` |
| `app_config_class` | derived | `AppConfig` subclass name |
| `app_verbose_name` | the project name | Shown in the Django admin |
| `project_description` | — | Used as the PyPI summary |
| `keywords` | derived | Comma separated, for PyPI |
| `initial_version` | `0.1.0` | |
| `author_name` / `author_email` | Vinta Software | Metadata and copyright notice |
| `github_org` / `github_repo` | `vintasoftware` / the distribution name | Project URLs |
| `license` | `MIT` | MIT, BSD 3-Clause, or proprietary |
| `copyright_year` | `2026` | |
| `python_min` | `3.10` | Floor of the support matrix |
| `django_min` | `5.2` | Floor of the support matrix |
| `test_django_main` | yes | Adds an informational Django `main` job |
| `publish_to_pypi` | yes | Adds `publish.yml` |

The support matrix is derived from the two floors and the interpreters each Django
release actually supports, so an unsupported pair is never emitted. Django 5.2 covers
Python 3.10 to 3.13; Django 6.0 covers 3.12 to 3.14. That mapping lives in
`python_support` in [copier.yml](copier.yml) and is the one place to edit when a new
release lands.

## Updating a generated project

Copier records the answers in `.copier-answers.yml`, so improvements to the tooling can
be pulled into projects that were generated earlier:

```bash
cd my-existing-package
uvx copier update --trust
```

## Working on the template

```
copier.yml                  questions, computed values, post-generation tasks
template/                   everything that gets copied into a new project
  {{ package_name }}/       the distributed package
  tests/                    the test project, including tests/testapp
```

Files ending in `.jinja` are rendered; everything else is copied verbatim. The GitHub
Actions workflows wrap their bodies in `{% raw %}` blocks, because Actions expressions
and Jinja both spell themselves `{{ }}`.

To try a change locally:

```bash
uvx copier copy --trust --defaults . /tmp/scratch-package
cd /tmp/scratch-package && uv sync --all-groups && uv run pytest
```

CI does exactly that on every push, across several answer combinations.

## Adding another license

`template/LICENSE.jinja` carries the full text for each choice. To add one, append a
branch there and extend the `license` choices plus the `license_classifier` and
`license_expression` maps in `copier.yml`.

## License

MIT. See [LICENSE](LICENSE).
