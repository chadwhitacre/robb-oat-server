# Development Setup

Want to use Robb Oat in your repo? [Install the app](https://github.com/apps/robb-oat). Want to hack on Robb Oat itself? Read on.

## OpenAI

[Sign up](https://beta.openai.com/signup) and [request access](http://beta.openai.com/codex-waitlist) to [Codex](https://beta.openai.com/docs/guides/code/introduction). You'll need your [Organization ID](https://beta.openai.com/account/org-settings) and an [API key](https://beta.openai.com/account/api-keys).

## GitHub

[Create your own dev GitHub App](https://github.com/settings/apps/new) with this config:

| Key | Value |
|:---|:--------|
| GitHub App name | some variation of "My Robb Oat Dev App" |
| Homepage URL | https://github.com/robb-oat/server |
| Webhook URL | whatever https://smee.io/new gives you |
| Permissions | Repository - "Read and Write" for Contents, Issues, and Pull Requests |
| Subscribe to events | Issue comment, Issues, Pull Request * |

Generate a private key. You'll use it to configure your local server in a minute. Also note your App ID.

[Fork this repo](https://github.com/robb-oat/server/fork) and install your dev
app on your fork. Turn on issues in your fork so you can test out Robb Oat
there.

<img width="320" alt="install example" src="https://user-images.githubusercontent.com/134455/196690422-b0eeeb07-796b-47c4-b418-2eac7699d56d.png">


## Local

To build (requires Python 3):

```
make
```

Required config in `local.env`:

```
OPENAI_ORG_ID=<see above>
OPENAI_API_KEY=<see above>
GITHUB_APP_ID=<see above>
GITHUB_APP_PRIVATE_KEY=<see above> # fold to one line, and don't include the '---- BEGIN/END ----'
GITHUB_BOT_USER_ID=<see below>
```

Here's what's up with the bot user ID. GitHub automatically provisions a bot
user account to act on behalf of your app. We want to constrain some of our
activity based on whether the bot is involved. For example, we only want the
bot to respond to reviews on its own pull requests. We have to subscribe to
_all_ pull request review comment events for the repo, so we need to filter
down to only comments on the bot's own PRs. There are some potential ways to
make this determination dynamically, but they feel brittle enough and the
stakes are high enough (it would suck if Robb Oat pushed commits for _every_
pull request comment in a busy repo) that we ask you to set this value
explicitly. You can find it by visiting:

https://api.github.com/users/your-app-slug[bot]

That's `your-app-slug` plus `[bot]`. Here is the link for the production Robb
Oat bot user to illustrate:

https://api.github.com/users/robb-oat[bot]

Grab the "id" from there and set it for `GITHUB_BOT_USER_ID`.

Optional config in `local.env`:

```
DJANGO_SENTRY_DSN=<go get one at sentry.io>
```

To setup the database:

```
./dev migrate
```

To run locally:

```
./dev
```

Install the [smee client](https://github.com/probot/smee-client) and start it like so:

```
$ smee --url https://smee.io/deadbeef --port 8000 --path /webhook
```

# Usage

Now you can try it out! Create an issue in your fork, and if all goes well you
should see a pull request from your dev app in under a minute. Robb Oat
attempts to fix all issues it understands, which are issues that start with a
link to code on GitHub followed by a blank line followed by instructions to
change the linked code.

For a good hello world create an issue with content like this:

```
https://github.com/${username}/${fork-name}/blob/${sha}/robboat/templates/homepage.html#L6

Say program instead of friend, and use a different emoji.
```

[Here's an example](https://github.com/chadwhitacre/robb-oat-server/issues/1).
To troubleshoot, use your smee.io dashboard or the "Advanced" tab on your dev
app.

<img width="640" alt="advanced tab" src="https://user-images.githubusercontent.com/134455/196694040-cf3006e4-34b5-43aa-99fe-95846939bcfe.png">

Happy hacking!
