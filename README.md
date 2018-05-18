# Tor Hidden Service Buildpack for Heroku
This buildpack sets up a Tor hidden service for your app on Heroku.

## Setup
Create a Heroku app as normal, with any buildpacks you typically use.
Then:
```bash
$ heroku buildpacks:add https://github.com/amivin/heroku-tor.git
```

With the buildpack installed, you'll need to modify your Procfile such that
the hidden service will be setup when the app runs.
```Procfile
web: ./tor/bin/run_tor <cmd you'd normally run>
```

While `web` works just fine, so too will any other process type. Use `web`
if you want the app to be accessible generally, as well as over Tor. Use
`<any other type>` (e.g. `foo`), to avoid Heroku's router routing to your app like so:
```Procfile
foo: PORT=9999 ./tor/bin/run_tor <cmd you'd normally run>
```

Your app will only be accessible over Tor, through your configured
`.onion` address.

## Variables
Of course, Tor hidden services require that you provide a private_key and it's
SHA, for the .onion name. You'll need to provide these as env vars:
* `HIDDEN_PRIVATE_KEY`: The contents of a private_key file
* `HIDDEN_DOT_ONION`: The onion name for the private_key.

#### How do you get these variables?
The easiest way is to:
```bash
heroku run bash
mkdir hidden
echo "HiddenServiceDir /app/hidden/" > tmptorrc
echo "HiddenServicePort 80 127.0.0.1:8000" >> tmptorrc
tor -f tmptorrc
cat hidden/*
^D
```
#### If you have a HIDDEN_PRIVATE_KEY and HIDDEN_DOT_ONION, set those now:
```bash
heroku config:set HIDDEN_PRIVATE_KEY=<YOUR HIDDEN KEY>
heroku config:set HIDDEN_DOT_ONION=<YOUR DOT ONION>
```
## Features
* Verifies integrity (see yourself how its done, I'm not sure its correct)
* Caches compilation
