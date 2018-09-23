# gatsby-plugin-google-gtag

Easily add Google Global Site Tag to your Gatsby site.

## Install

`npm install --save gatsby-plugin-google-gtag`

## How to use

```javascript
// In your gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: `gatsby-plugin-google-gtag`,
      options: {
        // You can add multiple tracking ids and a pageview event will be fired for all of them.
        trackingIds: ["YOUR_GOOGLE_ANALYTICS_TRACKING_ID", "YOUR_GOOGLE_ADWORDS_TRACKING_ID", "ETC"],
        // This object gets passed directly to the gtag config command
        // This config will be shared accross all trackingIds
        gtagConfig: {
          optimize_id: "YOUR_GOOGLE_OPTIMIZE_TRACKING_ID",
          anonymize_ip: true,
        },
        // This object is used for configuration specific to this plugin
        pluginConfig: {
          // Puts tracking script in the head instead of the body
          head: false,
          // Setting this parameter is also optional
          respectDNT: true,
          // Avoids sending pageview hits from custom paths
          exclude: ["/preview/**", "/do-not-track/me/too/"],
        }
      },
    },
  ],
}
```

## `<OutboundLink>` component

To make it easy to track clicks on outbound links in Google Analytics,
the plugin provides a component.

To use it, simply import it and use it like you would the `<a>` element e.g.

```jsx
import React
import { OutboundLink } from 'gatsby-plugin-google-gtag'

export default () => {
  <div>
    <OutboundLink
      href="https://www.gatsbyjs.org/packages/gatsby-plugin-google-gtag/"
    >
      Visit the Google Global Site Tag plugin page!
    </OutboundLink>
  </div>
}
```

## The "gtagConfig.anonymize_ip" option

Some countries (such as Germany) require you to use the
[\_anonymizeIP](https://support.google.com/analytics/answer/2763052) function for
Google Site Tag. Otherwise you are not allowed to use it. The option adds the
block of code below:

```javascript
function gtagOptout() {
  window['ga-disable-TRACKING_ID_1'] = true;
  window['ga-disable-TRACKING_ID_2'] = true;
  // etc
}
```

If your visitors should be able to set an Opt-Out-Cookie (No future tracking)
you can set a link e.g. in your imprint as follows:

`<a href="javascript:gtagOptout();">Deactive Google Tracking</a>`

## The "pluginConfig.respectDNT" option

If you enable this optional option, Google Global Site Tag will not be loaded at all for visitors that have "Do Not Track" enabled. While using Google Global Site Tag does not necessarily constitute Tracking, you might still want to do this to cater to more privacy oriented users.

## The "pluginConfig.exclude" option

If you need to exclude any path from the tracking system, you can add it (one or more) to this optional array as glob expressions.

## The "gtagConfig.optimize_id" option

If you need to use Google Optimize for A/B testing, you can add this optional Optimize container id to allow Google Optimize to load the correct test parameters for your site.