# Payment API

## Initialise the client

#### `new WhiteRabbitClient(whiterabbitConfig)`

Create a new client instance.

Arguments:

* `whiteRabbitConfig` — config object with attributes:
  * `host` — (optional) host of whiterabbit wallet instance. Possible options:
    * `https://wallet.whiterabbit.one`. (default). Production mode. Card deposits will require a real card. If `host` is not specified this mode will be used
    * `https://staging-wallet.whiterabbit.one`. Test mode. Card deposits can be tested with a test card (see below).
  * `campaign` — (optional) name of the campaign. Could be used to adapt look and feel of the payment popup

## Request payment

#### `client.requestPayment(imdbId, [pssAddress], [medium])`

Request a payment by movie's imdb ID. Example: `tt8367814`. Returns a Promise which resolves with the status of the payment.

Arguments:

* `imdbId` — `string`. IMDB ID for the movie.
* `pssAddress` — `string`. (Optional) A public address on XDAI Chain you want to receive your PSS share to. Every payment collected via your streaming site will be split between rightsholder, WhiteRabbit and you. This is your way to specify the address you want to receive your parts of the split to.
* `medium` — `string`. (Optional) One of the possible mediums. Defaults to "EST". Don't change it unless you understand what you are doing.

Response:

* `movieId` — `number`. Movie id as encoded by `utils.imdbToToken` function.
* `paymentId` — `string`. Transaction hash. Use block explorer to see the transaction [https://blockscout.com/xdai/mainnet/](https://blockscout.com/xdai/mainnet/)
* `status` — `boolean`. Result of the payment. `true` means the payment was successful, `false` — the payment was either declined by user or failure happened

Response example:

```
{
  movieId: "423557889"
  paymentId: "0x3202b4ef03ef29d87d81357e4c50105206be0396c5e5c4624d498b8cb7e2a083"
  status: true
}
```

## Get payment

**`client.getPayment(paymentId)`**

Returns all the information for a given `paymentId`.&#x20;

Response is the JSON object with the following fields:

* priceEUR — `string`. Payment amount in EUR. Converted from USD using the _current_ exchange rate (later will be changed to use exchange rate at the date of payment)
* priceUSD — `string`. Payment amount in USD
* splitUSD
  * rightsholder — `string`. Part of the payment distributed to the rightsholder (in USD)
  * whiterabbit — `string`. Part of the payment distributed to the WhiteRabbit as a fee (in USD)
  * pss — `string`. Part of the payment distributed to the PSS (in USD)
* pssAddr — `string`. Account address of the PSS where the payment took place
* pssName — `string`. PSS name as specified in RFR. May be empty if PSS haven't provided a name
* regionCode — `string`. Region code of the viewer in [two-letter ISO 3166-1 alpha-2 country code](https://en.wikipedia.org/wiki/ISO\_3166-1\_alpha-2) format.
* mediumCode — `string`. Medium name for the payment. For PSS it will likely be always `EST`
* movie
  * id — `number`. ID of the movie on themoviedb.org
  * imdbId — `string`. ID of the movie on imdb.com
  * title — `string`. Movie title in English. As provided by themoviedb.org
  * year — `string`. Year of the movie release. As provided by themoviedb.org
* status — `string`. Status of the payment in RFR. Possible values: `NEW` — collected, but not yet distributed. `COLLECTED` — distributed. `RETURNED` — returned to the viewer (excluding network and deposit fees which are not refundable)
* blockNumber — `number`. Block number on Gnosis Chain the transaction was included to
* timestamp — `number`. Timestamp of the block the transaction was included to
* sender — `string`. Account address of the viewer.
* txHash — `string`. Hash of the payment transaction on Gnosis Chain. Same as payment ID.

Response example:

```
{
  "blockNumber": 22571481,
  "mediumCode": "P2P",
  "movie": {
    "id": 85350,
    "imdbId": "tt1065073",
    "title": "Boyhood",
    "year": "2014"
  },
  "priceEUR": "1.9547612398771",
  "priceUSD": "2.1",
  "pssAddr": "0x0000000000000000000000000000000000000000",
  "pssName": null,
  "regionCode": "ME",
  "sender": "0xf6786661b2dbb3122ae448daf5eff4b886d423e6",
  "splitUSD": {
    "rightsholder": "0",
    "whiterabbit": "0",
    "pss": "0"
  },
  "status": "NEW",
  "timestamp": 1654784360,
  "txHash": "0xd6e100f8da5056eb85af2b92dce222d56472b86cb7d03684327a9c7f10331c7f"
}
```

## Get payments

**`client.getPayments(filter)`**

Returns all the payments matching the given filter.

The only argument `filter` is the optional object with one or more of the following attributes:

* pssAddr — `string`. Account address of the PSS who facilitated the payment
* movieId — \`string\`. IMDB id of the movie converted to tokenId. See [#utils.imdbtotoken-imdbid](payment-api.md#utils.imdbtotoken-imdbid "mention") &#x20;
* region — \`number\`. Region code of the viewer in [three digit M49 region code](https://en.wikipedia.org/wiki/UN\_M49) format
* status — `string`. Payment status (one on NEW, COLLECTED, RETURNED)
* medium — `number`. Medium code in three digit format (to be specified)
* timestamp — \`number\`. Timestamp of the payment
* sender — \`string\`. Account address of the viewer
* amount — `bigint`. Payment price as used in the payment transaction: `<price in usd> × 10^18`
* txHash — `string`. Transaction hash of the payment. Filtering by this is the same as [#get-payment](payment-api.md#get-payment "mention")
* blockNumber — `number`. Number of the block on Gnosis Chain transaction was included to

Response is a JSON array of the payments (see [#get-payment](payment-api.md#get-payment "mention") for payment attributes).

## Get payments (Advanced)

Instead of using convenience [#get-payment](payment-api.md#get-payment "mention") and [#get-payments](payment-api.md#get-payments "mention") functions, you can directly use RFR's GraphQL API.

Endpoints:

* production: https://api.whiterabbit.one/graphql
* test: https://staging-api.whiterabbit.one/graphql

Full query to POST to the API endpoint (authorisation is not required):

```
{
    payments {
      priceEUR
      priceUSD
      splitUSD {
        rightsholder
        whiterabbit
        pss
      }
      movie {
        id
        imdbId
        title
        year
      }
      regionCode
      mediumCode
      pssAddr
      pssName

      blockNumber
      status
      txHash
      sender
      timestamp
    }
  }
```

You can adjust the attributes you want. E.g.&#x20;

```
{
    payments {
      priceEUR
      priceUSD
      movie {
        imdbId
      }
      regionCode
      timestamp
    }
  }
```

Or/And use normal GraphQL filter possibilities. E.g:

```
{
    payments(where: { regionCode: 248, pssAddr: '0x...' }) {
      priceEUR
      priceUSD
      splitUSD {
        rightsholder
        whiterabbit
        pss
      }
      movie {
        id
        imdbId
        title
        year
      }
      regionCode
    }
  }
```

## Utilities

#### `utils.imdbToToken(imdbId)`

Convert given `imdbId` to WhiteRabbit token.

Arguments:

* `imdbId` — imdbID for the Movie.

#### `utils.tokenToImdb(tokenId)`

Convert given WhiteRabbit `tokenId` to imdbId.

Arguments:

* `tokenId` — tokenId for the MovieToken.

#### `utils.getRegionByCode(regionCode)`

Given a [three digit M49 region code](https://en.wikipedia.org/wiki/UN\_M49) returns a [two-letter ISO 3166-1 alpha-2 country code](https://en.wikipedia.org/wiki/ISO\_3166-1\_alpha-2)

Arguments:

* `regionCode` — M49 region code. Three digits, zero-padded (e.g. 008).
