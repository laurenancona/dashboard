Phila.gov Dashboard
=================

This is a [Dashing](http://shopify.github.com/dashing) dashboard for showing historical and realtime
Google Analytics from a number of websites. We use this to keep an eye on our various microsites
and web applications.

It shows historical Google Analytics session data for the last 7 days
(including today), and refreshes it every hour. It refreshes the realtime visitor
count every 10 seconds.

Here's an example with random data:

![alt tag](https://github.com/Code4SA/dashboard/blob/master/example.png)

Development
===========

Running locally
---------------

1. clone the repo
2. run `bundle install`
3. setup a Google API key (see below)
4. configure your websites in `websites.json`
5. run `rackup` and visit http://localhost:9292

Google API Key
--------------

You need to get a [Google OAuth 2.0 Service account key](https://developers.google.com/accounts/docs/OAuth2ServiceAccount)
and grant it access to the Google Analytics scope.

The Google API key comes in PKCS12 format. This must be converted to PEM format so
it can be injected via an environment variable.

    openssl pkcs12 -in Analytics-abc123.p12 -nodes -nocerts | fgrep -A 50 BEGIN > privatekey.pem

This is sensitive and shouldn't be in source control. So it will be injected using environment variables:

    export GOOGLE_API_KEY="`cat privatekey.pem`"

Take the email address generated for your API key `xxxx@developer.gserviceaccount.com` and give it
permissions on the Google Analytics accounts and/or properties you want to show in the dashboard.
Also set change the `google_account_id` variable in [jobs/google_analytics.rb](jobs/google_analytics.rb).

    google_account_id = 'xxx@developer.gserviceaccount.com'

Deployment
==========

This app runs on Heroku.

```bash
heroku config:set GOOGLE_API_KEY="`cat privatekey.pem`"
heroku config:set BASIC_AUTH_USER=a-username\
                  BASIC_AUTH_PASS=a-password
git push heroku
```

Remote Reloading
================

Occasional page refreshes help prevent JavaScript from pigging out on memory.
You can force all loaded dashboards to refresh manually by running
`curl -d '{ "auth_token": "YOUR_AUTH_TOKEN" }' \http://mydashboard.server.com/widgets/reload`
