---
description: >-
  Integrate your streaming website with White Rabbit and collect payments
  officially
---

# Monetise your streaming site by becoming a White Rabbit Streaming Partner.

You own a streaming site and would like to collect payments from your audience while staying in a legal zone? Here is how you integrate with White Rabbit payment solution and start collecting payments shared with films' rightsholders.



## Set up a cryptographic wallet.

You will receive a share from each of the payments on this account.

1. Install the wallet. The easiest is to use a [MetaMask wallet](https://metamask.io/) as browser extension. Make sure you backed up your seed phrase in a secure place.
2. Add Gnosis Chain network to the wallet. Go to [https://chainlist.org/chain/100](https://chainlist.org/chain/100), click Connect-Wallet and then click Add-to-Metamask. You should be able to switch your MetaMask to Gnosis Chain now. White Rabbit is using Gnosis Chain to transact.
3. Take note of your account address in MetaMask (a string starting with 0x..). You will need it later

## Add the White Rabbit payment client to your site

White Rabbit payment client is a small Javascript library with a simple API. Use it on all the pages you want to request payment on. Most likely it will be the page with a movie player.

### If you use NPM in your project

Install `@whiterabbitjs/client` package:

```
npm install --save @whiterabbitjs/client
```

Then can get access to `WhiteRabbitClient` class on your webpage like this:

```
import { WhiteRabbitClient } from '@whiterabbitjs/client';
```

### If you don't use NPM

You can download the WR payment module file to your site's assets.

Get latest package tarball

```
curl https://registry.npmjs.org/@whiterabbitjs/client/ | jq '.versions[."dist-tags".latest].dist.tarball' | xargs curl -O
```

Extract the `package/dist/index.min.mjs` file from the package and add to your assets by meaninful name (e.g. `whiterabbit-client.min.mjs`. If you don't use Javascript modules, there are other bundles in the package as well.

Import the module in your code, so you can request the payment like this:

```
import { WhiteRabbitClient } from './whiterabbit-client.min.mjs';
```

{% hint style="info" %}
Instead of downloading the file you can use unpkg CDN (or similar) to load the file for you like this:

```
import { WhiteRabbitClient } from 'https://unpkg.com/@whiterabbitjs/client@2.40.1/dist/index.min.mjs';
```
{% endhint %}

## Request the payment from user

When you want to ask the user to pay for the movie, just call the `paymentRequest` method of the `WhiteRabbitClient` instance like this:

```
// change this import if you don't use NPM (see above)
import { WhiteRabbitClient } from '@whiterabbitjs/client';

const client = new WhiteRabbitClient();

const pssAccountAddress = "0x..."; // use your account address here
const movieId = 'tt8367814'; // IMDB ID of the movie you want to request payment for

const result = await client.requestPayment(movieId, pssAccountAddress);

if (result.status) {
    console.log(`Payment succeed. Payment ID: ${result.paymentId}`);
    // payment is successful. You can start the video playback or do something else
} else {
    console.log("Payment has been either declined or failed");
}
```

White Rabbit doesn't impose on you any restrictions when to ask for the payment and what to do after the payment is processed. You have full freedom.

## Getting payment details

Once the payment happened, you may want to store the `paymentId` on your side for reference.

At any point you can use [#get-payment](../api-reference/payment-api.md#get-payment "mention") API to get information about the payment.

E.g.

```
const payments = await client.getPayment("0x....")
```

## Getting all the payments

To get all the payments your users made via your site, use [#get-payments](../api-reference/payment-api.md#get-payments "mention") API with the filter by `pssAddr`. Use your wallet address here (the one you used in `requestPayment` function (see [#request-the-payment-from-user](setting-up-a-partner-streaming-site-pss.md#request-the-payment-from-user "mention")).

E.g. if your account address is `0x0Cc569107A7BDCf9170a49d6E0B71945ae34C7F0` then the call will be the following:

```
const payments = await client.getPayments({ pssAddr: "0x0Cc569107A7BDCf9170a49d6E0B71945ae34C7F0" });
```

Optionally you can mix in any other possible filtering options (see filter definition in [#get-payments](../api-reference/payment-api.md#get-payments "mention")). For example to get all the payments which was distributed already:

```
const payments = await client.getPayments({ status: COLLECTED, pssAddr: "0x0Cc569107A7BDCf9170a49d6E0B71945ae34C7F0" });
```
