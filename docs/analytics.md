# Analytics for Onion Launchpad

## Support

Onion Launchpad supports a basic analytics gathering based on [Matomo][]:

1. The feature is *disabled* by default, and enabled only if some environment
   variables are set.
2. This feature, even with a better configuration in terms of privacy, could
   still be a point of collecting access data without passing to the Tor
   network for better anonymization. And also would rely on additional JavaScript
   code embedded in the landing page.
3. Services operators are be recommended to host the backend only behind an
   HTTPS proxy without IP logging (and without passing the source IP to the
   backend, so if there's any backend vulnerability it won't be possible to
   attackers to discover user's IP addresses). ___Or even better: leave the
   backend behind an Onion Service___, and expose only the frontend proxies
   through public-accessible IP addresses.
4. There is also a [consent UX][] informing users what and how it's
   collected, and asking for authorization. No cookies reside in the
   client machine.
5. The analytics collection is bypassed entirely if the [Do Not Track][]
   configuration is set. Onion Launchpad respects this setting
   [even if it's considered deprecated][].

## Security

It's also worth noting that adversaries could attack the analytics system by:

0. Intercepting DNS Queries in the form of `myanalytics.example.org` that may
   disclose information:
    * That an analytics platform exists.
    * That the user is giving information to this platform.
1. Adding fake access data into the metrics system.
2. Censoring the analytics endpoint so the user browser cannot reach it in
   order to send access data.
3. DDoS'ing the analytics backend.
4. Exploiting vulnerabilities in the backend to extract data.

## Setting up

The analytics functionality is controlled by these environment variables:

* `LEKTOR_ANALYTICS`: when set to `1`, enables the analytics collection.
* `LEKTOR_ANALYTICS_SDK`: set to `cleaninsights` to use the [CleanInsights JS SDK][]
  instead of the default Matomo's [Javascript Tracking Client][]. The [CleanInsights JS SDK][]
  is more robust and privacy-aware, but have a few caveats, has experimental support
  and might now work well. Refer to [tpo/onion-services/onion-launchpad#87][]
  for details.
* `LEKTOR_ANALYTICS_BACKEND`: contains the base URL of the Analytics instance.
  Example: `https://myanalytics.example.org/`. Onion Launchpad will then point
   to both `https://myanalytics.example.org/matomo.js` and
  `https://myanalytics.example.org/matomo.php`.
* `LEKTOR_ANALYTICS_SITE_ID`: should be set to the Analytics `siteId` configured
   in the backend.
* `LEKTOR_ANALYTICS_LINK_TRACKING`: when set to 1, activates Matomo's
  `enableLinkTracking` setting, otherwise keep it off.
  Ignored when using the [CleanInsights JS SDK][].
* `LEKTOR_ANALYTICS_BROWSER_DETECTION`: when set to 1, enables
  browser feature detection. For the Matomo backend, keeping this
  setting off will enforce the `disableBrowserFeatureDetection` option.
  You might want to turn this on if your Matomo backend is too
  old, since [disabling of browser detection was implemented only recently][].
* `LEKTOR_ANALYTICS_PRIVACY_POLICY_CONTACT`: the contact information for the
  privacy policy, as per [best practices on analytics gathering][]. It can
  be an e-mail address or URL.
* `LEKTOR_ANALYTICS_BACKGROUND_COLOR`: the background color for the [consent UX][],
  in hexadecimal format (`FFFFFF`).

Check Matomo's [JavaScript Tracking Client][] and the [CleanInsight JS SDK][]
documentations for details.

[CleanInsights JS SDK]: https://gitlab.com/cleaninsights/clean-insights-js-sdk#easy-tracking-of-websites-with-the-autotracker-version
[tpo/onion-services/onion-launchpad#87]: https://gitlab.torproject.org/tpo/onion-services/onion-launchpad/-/issues/87

## Privacy Policy

Onion Launchpad comes with a [hard-coded Privacy Policy text][] following the
[best practices on analytics gathering][]. This text is automatically compiled
in a `/policy` link.

The policy comes in two flavours:

1. The default, covering the basic use-case of Onion Launchpad being deployed
   in a third party service provider that may collect IP addresses.

2. A special policy version when analytics is enabled, and ready to be used
   with [Matomo][] or [Clean Insights][].

Currently there's no way to customize this policy beyond these two flavors.

If you plan to collect analytics, we recommend that you use a
[Matomo][]-compatible instance that does not log IP addresses if you plan to
collect analytics in a way that respects the user's privacy.

In any case, please check if the Privacy Policy text matches your analytics
platform.

[Matomo]: https://matomo.org
[consent UX]: https://okthanks.com/blog/2021/5/14/clean-consent-ux
[Do Not Track]: https://en.wikipedia.org/wiki/Do_Not_Track
[even if it's considered deprecated]: https://developer.mozilla.org/en-US/docs/Web/API/navigator/doNotTrack
[JavaScript Tracking Client]: https://developer.matomo.org/api-reference/tracking-javascript
[disabling of browser detection was implemented only recently]: https://github.com/matomo-org/matomo/pull/18599
[best practices on analytics gathering]: https://matomo.org/blog/2018/04/how-should-i-write-my-privacy-notice-for-matomo-analytics-under-gdpr/
[hard-coded Privacy Policy text]: https://gitlab.torproject.org/tpo/onion-services/onion-launchpad/-/blob/main/content/policy/contents.lr
[Clean Insights]: https://cleaninsights.org
