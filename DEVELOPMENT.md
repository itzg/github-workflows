## Testing

Install [act](https://nektosact.com/)

### issues-notify-discord

1. Go to <webhook.site> and allocate a web url.
2. Set that as `discordWebhook` in `.env` file.

#### Regular Pull Requests

```shell
act pull_request_target \
  -W .github/workflows/issues-notify-discord.yml \
  -j labelNotify \
  -e tests/issues-notify-discord/pull-request-closed.json \
  --secret-file .env
```

#### Pull Requests with Dependabot, etc

```shell
act pull_request_target \
  -W .github/workflows/issues-notify-discord.yml \
  -j labelNotify \
  -e tests/issues-notify-discord/pull-request-closed-with-dependencies.json \
  --secret-file .env
```
