## Purpose
Give concise, repo-specific guidance so an AI coding agent can be productive quickly in this Django project.

## Big picture (what this repo is)
- A small Django 3.2.x social media template app (project: `social_book`, app: `core`).
- Database: SQLite file at `db.sqlite3` (development). Static and media assets live in `static/` and `media/` respectively.
- Templates are in the repository `templates/` and derive from the upstream template: `tomitokko/django-social-media-template` (see root `README.md`).

## Key files to read first
- `social_book/settings.py` — dev configuration, STATICFILES, MEDIA, and Django version comment.
- `manage.py` — typical Django entrypoint (runserver, migrations, tests).
- `core/models.py` — canonical domain model (Profile, Post, LikePost, FollowersCount). Important: many relations are stored as plain strings rather than ForeignKeys.
- `core/views.py` — main web flows: index, upload, search, like_post, profile, follow, settings, signup/signin/logout. Uses `login_required` heavily.
- `core/urls.py` and `social_book/urls.py` — routing and media static serving in development.

## Important repo-specific conventions and pitfalls
- Denormalized data model: `Post.user` is a CharField containing username (not a ForeignKey). `LikePost.post_id` is a CharField referencing `Post.id` (Post.id is a UUID). `Profile` both FK to User and an `id_user` int column. When editing models, preserve these shapes or migrate carefully (migrations and existing data depend on strings).
- Primary key: `Post.id` is a UUIDField primary key. Queries that expect integer IDs will break.
- File uploads: `Post.image` uses `upload_to='post_images'`; `Profile.profileimg` uses `profile_images` folder. Media root is `MEDIA_ROOT` set in `social_book/settings.py`.
- Templates and static assets are relied on heavily for UI; many JavaScript/CSS assets live under `static/` and `static/settings/`.

## Common developer workflows (explicit commands)
Use PowerShell on Windows (examples below assume the repo root).

1) Create and activate a venv, install Django (project comments indicate Django 3.2.6):
   python -m venv .venv
   .\.venv\Scripts\Activate.ps1; pip install Django==3.2.6

2) Run migrations and start dev server:
   python manage.py migrate
   python manage.py runserver

3) Create a superuser (for admin):
   python manage.py createsuperuser

4) Run tests (if adding or changing behavior):
   python manage.py test

Notes: there is no `requirements.txt` in the repo. Pin dependencies when adding packages and add a requirements file.

## Examples of code patterns to follow
- Views: use `@login_required(login_url='signin')` for routes requiring auth (see `core/views.py:index` and most other views).
- Search/suggestions: user suggestion code uses `itertools.chain` and list comprehensions — preserve this style when modifying search logic.
- Like/unlike flow: `LikePost` objects are created/deleted and `Post.no_of_likes` is manually incremented/decremented; look at `like_post` for exact behavior to mirror.

## Integration points & external assets
- Templates from `tomitokko/django-social-media-template` (root README). Keep template structure when changing front-end.
- Static libs (fullcalendar, select2, bootstrap, etc.) under `static/settings/` — front-end changes may need updates here.

## Migrations and data safety
- This repo contains several migrations in `core/migrations/`. When changing models, create new migrations and be careful converting string-based relations to FKs — include data migrations and tests.

## Security & deployment notes (discoverable from repo)
- `social_book/settings.py` contains a SECRET_KEY and DEBUG=True. Do not commit production secrets. If preparing for deploy, move secrets to env vars and set DEBUG=False.

## Quick checklist for PRs an AI might open
- Keep existing field shapes (strings for usernames/post ids) unless you add a data migration that preserves data.
- Add or update `requirements.txt` when introducing new dependencies.
- Run `python manage.py migrate` and `python manage.py test` locally; confirm `runserver` serves media in dev (urls.py uses `static()` for MEDIA).
- If touching templates, confirm static assets under `static/` are still referenced correctly.

## Where to look for further context
- `core/models.py`, `core/views.py`, `core/urls.py`, `social_book/settings.py`, `manage.py`, `templates/`, `static/`, `media/`

If you want, I can iterate this file with additional examples (small code snippets or common PR templates) — tell me which sections to expand or any infra/dependency info I missed.
