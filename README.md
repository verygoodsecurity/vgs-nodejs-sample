<p align="center"><a href="https://www.verygoodsecurity.com/"><img src="https://avatars0.githubusercontent.com/u/17788525" width="128" alt="VGS Logo"></a></p>
<p align="center"><b>vgs-nodejs-sample</b></p>
<p align="center"><i>Integration of node.js app with VGS</i></p>

## Requirements
- VGS [account](https://dashboard.verygoodsecurity.com/)
- Stripe [account](https://dashboard.stripe.com/register)
- HomeAway [account](https://www.homeaway.com/platform/lead-form)
- [ngrok](https://ngrok.com/)

## Third-party services
1. Create account on Stripe
2. Generate Stripe sandbox API key
3. Create account on HomeAway
4. Get your HomeAway client_id and secret

## VGS base setup
1. Go to [VGS-Dashboard](https://dashboard.verygoodsecurity.com), create a new organization, create a new vault.
2. Select your vault, go to `Integration` page
3. Copy your proxy URL, it should be in format:
```
http://<user>:<password>@<tenant>.SANDBOX.verygoodproxy.com:8080
```
* Proxy URL should start with `http://`, otherwise `https-proxy-agent` lib won't work
* This apllication contains SSL certificate `cert.pem` for VGS Sandbox environment only

## Run application
1. Clone repository and go to `app` folder
2. Install all dependencies `npm install`
3. Run the app 
```HA_CLIENT='<client_id>' HA_SECRET='<secret_key>' STRIPE_TOKEN='<token>' HTTP_PROXY='<proxy_url>' npm start```
4. Run ngrok `ngrok http 3000`
5. Copy provided address `https://<some_id>.ngrok.io`

## Application structure
- `/` - search form, after selecting some hotel will redirect to the payment page
- `/list` - list of payments for booked hotels
- `/list/:id` - revealed payment data page

## Secure inbound traffic with VGS
<img src="https://github.com/verygoodsecurity/vgs-nodejs-sample/raw/master/images/redaction.gif">

1. Go to VGS dashboard
2. Go to `Secure traffic` -> `Inbound`
3. Put ngrok url to upstream
4. Apllication should be availiable by `https://<tenant>.sandbox.verygoodproxy.com/`
6. Fill forms in app, submit payment data
7. Open VGS dashboard, go to `Logs`
8. Ensure that logger is recording payloads
9. Find the request with payment data, click on it
10. Click on `Secure this payload`
11. Select fields, click `Secure`
12. Done, you inbound secure route has been created and should look like this:
<img src="https://github.com/verygoodsecurity/vgs-nodejs-sample/raw/master/images/redaction.png">

## Setup self-revealing
<img src="https://github.com/verygoodsecurity/vgs-nodejs-sample/raw/master/images/self_revealing.gif">

1. Open payments list page at `https://<tenant>.sandbox.verygoodproxy.com/list`
2. All fields should be replaced with tokens
3. Click on `Reveal`, nothing should change because revealing rule isn't setup yet
4. Open VGS dashboard, go to `Routes`
5. Click `New Route` -> `New inbound route`
6. Put ngrok url to upstream
7. Set one filter condition: `Pathinfo matches /list/\\d+`
8. Phase: `On response`
9. Basic type: `REVEAL`
10. Operation: `RegExp`
11. Fields in Regexp: `tok_[a-z]*_[0-9a-zA-Z]{20,22}`
12. Click Save
13. Go back to the payments page at `https://<tenant>.sandbox.verygoodproxy.com/list`
14. Click on `Reveal`, now instead of tokens you should see real data there
13. Done, you inbound self-revealing route has been created correctly and should look like this:
<img src="https://github.com/verygoodsecurity/vgs-nodejs-sample/raw/master/images/self_revealing.png">

## Secure outbound traffic to Stripe
<img src="https://github.com/verygoodsecurity/vgs-nodejs-sample/raw/master/images/third_revealing.gif">

1. Go to VGS dashboard
2. Go to `Secure traffic` -> `Outbound`
3. Switch to the application, submit payment data again
4. Go back to VGS dashboard, it should detect the outbound connection, click on `Secure data`
5. Find the request to Stripe API `https://api.stripe.com/v1/tokens`, click on it
6. Click on `Secure this payload`
7. Select fields, click `Secure`
8. Done, you outbound secure route has been created and should look like this:
<img src="https://github.com/verygoodsecurity/vgs-nodejs-sample/raw/master/images/third_revealing.png">

## What is VGS?

_**Want to just jump right in?** Check out our [getting started
guide](https://www.verygoodsecurity.com/docs/getting-started)._

Very Good Security (VGS) allows you to enhance your security standing while
maintaining the utility of your data internally and with third-parties. As an
added benefit, we accelerate your compliance certification process and help you
quickly obtain security-related compliances that stand between you and your
market opportunities.

To learn more, visit us at https://www.verygoodsecurity.com/

## License

This project is licensed under the MIT license. See the [LICENSE](LICENSE) file
for details.