

### package release:

Checklist for publishing out to crates.io, TODO: move this into a GitHub Action

- Bump `Cargo.toml` version
- Update `CHANGELOG.md` with new version
    - Follow guidance from https://keepachangelog.com/
- Make sure current release is ready:
    - `cargo publish --dry-run`
- Tag the release in git:
    - `GIT_COMMITTER_DATE=$(git log -n1 --pretty=%aD) git tag -a -m "Release 0.3.0" 0.3.0 && git push --tags`
- Publish to crates.io
    - `cargo publish`
