# Google Ads tracking — Axion Motors website

## What is installed
- Google Ads base tag: `AW-16996579305` in `index.html` `<head>`.
- This is the Google Ads account **668-852-5393**, containing the campaign `Website traffic-Performance Max-2` whose final URL is `https://axionmotorsus.com/`.
- The base tag supports page visit measurement and tag detection. It does **not** by itself confirm a sales inquiry or fix every conversion-goal diagnostic.

## Important: the contact form is not a real submission endpoint
The current contact form uses `action="mailto:info@axion-motors.com"`. Clicking **Send Inquiry** asks the visitor's device to open an email composer. The website cannot know whether the visitor sent the email, whether it reached the inbox, or whether the email client opened successfully.

Therefore this change deliberately **does not** trigger the existing Google Ads `Contact` event (`ads_conversion_Contact_1`) on the submit button. Tracking clicks as successful leads would inflate conversions and corrupt Performance Max bidding.

## To implement real lead conversions
1. Replace the `mailto:` handler with a proper HTTPS form submission endpoint (such as a Cloudflare Pages Function with a securely configured email delivery provider). Never put provider API keys in public HTML or GitHub source.
2. Return an explicit success response only when the form has been accepted and reliably processed.
3. On that verified success response only, invoke the Google Ads event snippet that belongs to the **correct conversion action**. In the current Ads UI, `Contact` uses event name `ads_conversion_Contact_1`; verify it in the Ads UI before wiring the event.
4. Set campaign-specific conversion goals for Axion Motors. This Google Ads account is shared with Mansion RV Solar; don't accidentally optimize Axion for solar leads or count the same inquiry twice.
5. Avoid transmitting customer names, email addresses, phone numbers, and free-text form content to analytics. Review consent and privacy notice requirements before launching tracking.

## How to test after deployment
- Load `https://axionmotorsus.com/` in a browser.
- In DevTools Network, confirm the browser requests `https://www.googletagmanager.com/gtag/js?id=AW-16996579305` (unless blocked by a privacy extension or consent configuration).
- Use [Google Tag Assistant](https://tagassistant.google.com/) to confirm the tag ID is recognized.
- Check Google Ads tag diagnostics after processing. A tag may be detected while the `Contact` conversion still reads `Awaiting conversions`, because no verified lead event has been sent.
