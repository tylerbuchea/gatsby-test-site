# Gatsby Test Site

## Setup

```bash
yarn
```

## Develop

```bash
yarn develop
```

## Test

**How we going to test this?**

I'm not really sure of any easy way to automate testing of Google events.

There are three main products that have all converged on GTAG.

> Each supported product generates tags that can be copied and pasted into web pages. To learn how to locate the snippet for a product, see the product- specific documentation: (Analytics, Google Ads, Campaign Manager, Search Ads 360). - https://developers.google.com/gtagjs/devguide/snippet

You can read more about how to implement all of these different products
side by side in the article quoted below:

> Adapt an existing global site tag for multiple Floodlight configurations or Google products - https://support.google.com/dcm/partner/answer/7568534

I know that both Google Analytics and Adwords have a "pageview" event (not sure about Marketing Platform and need to find out)
so it makes sense to hook into "onRouteUpdate" in Gatsby
and fire off a "pageview" event no matter what trackignId has been entered into the configuration.

I've read some articles that say you need to specify the trackingId of the product you're sending the event to explicitly, but I read
another article that said as long as you've done the initial conifg with multiple trackingIds that firing a gtag event without a specific
`send_to` address will just send the event to all configured products.

ie:

```js
// We know this is the proper way to configure gtag for multiple products
gtag('config', 'GA-TRACKING_ID')
gtag('config', 'AW-CONVERSION_ID')

// and we know this will send a pageview to AW-CONVERSION
gtag('event', 'pageview', { send_to: 'AW-CONVERSION_ID' })

// but we don't know if this sends a pageview to both of the products
gtag('event', 'pageview')
```

```js
const options = {
  // Make sure all of these are register correctly
  // How:
  trackingIds: [
    'GA-TRACKING_ID', // Google Analytics / GA
    'AW-CONVERSION_ID', // Google Ads / Adwords / AW
    'DC-FLOODIGHT_ID', // Marketing Platform advertising products (Display & Video 360, Search Ads 360, and Campaign Manager)
  ],

  gtagConfig: {
    // Make sure the optimize id is registered correctly for all trackingIds
    // How?
    optimize_id: 'OPT_CONTAINER_ID',
    // Make sure this enables anonymous ips for all trackingIds
    // Make sure this generates the gtaOptout function and that the function that side effect produces is works for all trackingIds
    // How?
    anonymize_ip: true,
  },

  pluginConfig: {
    // Make sure this puts all the injected scripts in the head when true and footer when false
    head: false,

    // This should never register the window.gtag function or send any data to Google
    // How? if (respectDNT) expect(window.gtag).toEqual(undefined);
    respectDNT: true,

    // Any path matching any of the regular expressions in this list should not send a pageview event to Google
    exclude: ['/preview/**', '/do-not-track/me/too/'],
  },
}
```

## Recreate (if you want for some reason)

```bash
npm install -g gatsby-cli
gatsby new gatsby-site https://github.com/gatsbyjs/gatsby-starter-default

cd gatsby-site
mkdir src/plugins
# Then add gatsby-plugin-google-gtag folder to src/plugins
```

**Then add plugin config to gatsby-config.js**

```js
{
  resolve: `gatsby-plugin-google-gtag`,
  options: {
    trackingIds: ['UA-59245528-21'],
    gtagConfig: {},
    pluginConfig: {},
  },
},
```

## Resources

- [https://developers.google.com/analytics/devguides/collection/gtagjs/renaming-the-gtag-object](https://developers.google.com/analytics/devguides/collection/gtagjs/renaming-the-gtag-object)
- [https://www.en.advertisercommunity.com/t5/Google-Ads-Tracking-and/Global-site-tag-Adwords-vs-Analytics/td-p/1244711#](https://www.en.advertisercommunity.com/t5/Google-Ads-Tracking-and/Global-site-tag-Adwords-vs-Analytics/td-p/1244711#)
- [https://stackoverflow.com/questions/51486130/how-to-combine-google-analytics-and-google-adwords-gtag-js](https://stackoverflow.com/questions/51486130/how-to-combine-google-analytics-and-google-adwords-gtag-js)
- [https://developers.google.com/gtagjs/devguide/snippet](https://developers.google.com/gtagjs/devguide/snippet)
- [https://support.google.com/analytics/answer/7478520?hl=en](https://support.google.com/analytics/answer/7478520?hl=en)
- [https://support.google.com/dcm/partner/answer/7568534](https://support.google.com/dcm/partner/answer/7568534)
- [https://support.google.com/optimize/answer/7513085](https://support.google.com/optimize/answer/7513085)
- [https://developers.google.com/analytics/devguides/collection/gtagjs/ip-anonymization](https://developers.google.com/analytics/devguides/collection/gtagjs/ip-anonymization)
- [https://developers.google.com/analytics/devguides/collection/gtagjs/user-opt-out](https://developers.google.com/analytics/devguides/collection/gtagjs/user-opt-out)
- [https://developers.google.com/analytics/devguides/collection/analyticsjs/debugging](https://developers.google.com/analytics/devguides/collection/analyticsjs/debugging)
- [https://support.google.com/dcm/partner/answer/7570440?hl=en](https://support.google.com/dcm/partner/answer/7570440?hl=en)
- []()
