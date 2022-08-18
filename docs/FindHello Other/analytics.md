Both Google Analytics and Facebook Pixel are set up to track ad attibution and/or event tracking in the app on both web and mobile.

## Google Analytics

Google Analytics is set up using the web Javascript library only. In other words
we're not using the Cordova native library for the device, we're using the standard 
`gtag.js` library for both device and web. To enable analytics for a build, you 
simply need to make sure the `GOOGLE_ANALYTICS_ID` is present in your environment
variables. Once the app detects that env var it will start sending analytics.

The actual insertion of the library is handled via an Ionic provider. You can 
see the code in the app in `src/providers/google-analytics/google-analytics.js`

To add new events in the code you simply need:

```js
...

// The path will be different depending on the file you are importing to
import { GoogleAnalyticsProvider } from '../providers/google-analytics/google-analytics';

...

constructor(
    ...
    public googleAnalytics: GoogleAnalyticsProvider
) {}

...


this.googleAnalytics.send('event', 'my-event');
```

The values for each account are:

Adwords: `239-487-1756`
Web: `UA-56057294-4`
Mobile: `UA-56057294-3`

More information here:

- [https://developers.google.com/analytics/devguides/collection/gtagjs/events#send_events](https://developers.google.com/analytics/devguides/collection/gtagjs/events#send_events)
- [https://support.google.com/analytics/answer/1033068#Anatomy](https://support.google.com/analytics/answer/1033068#Anatomy)

### Developing locally

When developing locally, you should make sure that no analytics or adwords IDs
are included in the `enviromnets.ts`:

```
API_URL="http://localhost:8000/",
GOOGLE_MAPS_API_KEY="AIzaSyD-3QcVnMPPiqAkKXoPvnz-nKEKLLqy2Xc"
```

### Deploying web

The webapp is deployed to staging and production automatically by CircleCI. This 
is configured to automatically insert the relevant environment variables to the
build for production. No analytics are recorded on the staging server.

### Building Android and iOS for deployment

When building the mobile apps on your machine, you need to make sure that the
correct analytics and adwords IDs are in the `environment.prod.ts` file:

```
API_URL="https://resources.therefugeecenter.org/",
GOOGLE_MAPS_API_KEY=(lookup in Google cloud account),
GOOGLE_ANALYTICS_ID="UA-56057294-3",
GOOGLE_ADWORDS_ID="239-487-1756"
```

## Facebook Pixel

The Facebook tracking pixel is more straight forward. An `img` tag is simply 
inserted into the app with the Facebook Pixel ID.

You can see this in the app in `src/app/app.html`.

Like the Google Analytics, this is controlled via an env variable. So to 
enable it, add the following to the `environment.prod.ts`:

```
...
FACEBOOK_PIXEL_ID="1708890232460225"
```

Again, this is automatically handled for web buils via CircleCI.
