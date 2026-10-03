# Deployment

Deploy from this checkout with the installed `pp` CLI. Its source is the sibling `pp-ployploy-deployment-cli` repository; run `make install` there to update it.

```sh
go test ./...
pp plan
pp deploy
pp status
```

Commit reviewed changes before deploying. pp builds the working tree, including uncommitted files. Release-specific image tags keep repeated builds of a commit separate.

`deploy.yml` owns the host, project name, route and health checks. The public `/health` smoke check verifies both the route and database access. It must use GET, because this handler rejects HEAD.

Keep `.env` private with `JWT_SECRET` set to at least 32 characters. Never copy it or `.deploy/` into another project. pp stores local release history under `.deploy/`; `pp status` is the authoritative way to inspect the active release after state migrations.

The existing Compose project is `gotths-example`. SQLite and runtime configuration persist in the named Docker volume `gotths-data`, mounted at `/data`. Do not rename that volume or delete it during an update. The entrypoint applies packaged migrations and refreshes packaged assets without replacing the database or existing runtime configuration.

The Dockerfile pins goose to v3.26.0 for the Go 1.23 build. Upgrading goose requires checking its Go version requirement.
