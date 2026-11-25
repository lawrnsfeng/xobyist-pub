# xobyist release build

Built from lawrnsfeng/xobyist@fd8cc3a5a3d4818af3f95b7086e201c0322eff27.

- `server`: Linux x86-64 server binary. Migrations and the seed game catalog are built in.
- `web/`: the web app. The server serves it together with the `/api` it calls,
  so both run as one program on one address.

This repository is the build output only, rewritten on every release. GitHub
cannot run the server, and GitHub Pages cannot host `web/` on its own (the app
needs the server's `/api` on the same address). Run it on your own Linux machine.

## Try it

```sh
git clone https://github.com/lawrnsfeng/xobyist-pub && cd xobyist-pub
XO_ADMIN_PASSWORD=change-me XO_DIST=web ./server
```

Open http://127.0.0.1:3000 and log in as owner with that password.

## Host it

1. Run the server on a Linux x86-64 machine, listening locally:

   ```sh
   XO_ADMIN_PASSWORD='a long secret' XO_DIST=web \
   XO_DB='sqlite:///var/lib/xobyist/xobyist.db?mode=rwc' XO_MEDIA=/var/lib/xobyist/media \
   XO_SECURE_COOKIE=1 XO_TRUST_PROXY=1 ./server
   ```

   Keep the database and media outside this checkout, so updating the build
   never touches your data.

2. Put HTTPS in front with a reverse proxy, for example Caddy:

   ```
   games.example.com {
       reverse_proxy 127.0.0.1:3000
   }
   ```

   `XO_SECURE_COOKIE=1` needs HTTPS, and `XO_TRUST_PROXY=1` makes the server
   read the visitor's address from the proxy (only set it behind your own proxy).

3. Keep it running with systemd or similar, restarting on failure.

## Update

Each release replaces this repository's history, so reset rather than pull,
then restart the server:

```sh
git fetch origin && git reset --hard origin/master
```

## Settings

| Variable | Default | Meaning |
|---|---|---|
| `XO_ADMIN_PASSWORD` | (required) | Owner login password |
| `XO_DIST` | `dist` | Web app directory; use `web` here |
| `XO_BIND` | `127.0.0.1:3000` | Listen address |
| `XO_DB` | `sqlite://xobyist.db?mode=rwc` | SQLite database |
| `XO_MEDIA` | `media` | Game images added at runtime |
| `XO_SECURE_COOKIE` | off | `1` behind HTTPS |
| `XO_TRUST_PROXY` | off | `1` behind your own reverse proxy |
| `BGG_TOKEN` | unset | Enables BoardGameGeek search in Owner → Games |
