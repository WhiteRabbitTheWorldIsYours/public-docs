# Payment API

#### `new WhiteRabbitClient(whiterabbitConfig)`

Create a new client instance.

Arguments:

* `whiteRabbitConfig` — config object with attributes:
  * `host` — (optional) host of whiterabbit wallet instance. Possible options:
    * `https://wallet.whiterabbit.one`. (default). Production mode. Card deposits will require a real card. If `host` is not specified this mode will be used
    * `https://staging-wallet.whiterabbit.one`. Test mode. Card deposits can be tested with a test card (see below).
  * `campaign` — (optional) name of the campaign. Could be used to adapt look and feel of the payment popup

#### `client.requestPayment(imdbId, [pssAddress], [medium])`

Request a payment by movie's imdb ID. Example: `tt8367814`. Returns a Promise which resolves with the status of the payment.

Arguments:

* `imdbId` - imdb ID for the movie.
* `pssAddress` - (Optional) A public address on XDAI Chain you want to receive your PSS share to. Every payment collected via your streaming site will be split between rightsholder, WhiteRabbit and you. This is your way to specify the address you want to receive your parts of the split to.
* `medium` - (Optional) One of the possible mediums. Defaults to "EST". Don't change it unless you understand what you are doing.

Response:

* `movieId` — Number. Movie id as encoded by `utils.imdbToToken` function.
* `paymentId` - String. Transaction hash. Use block explorer to see the transaction [https://blockscout.com/xdai/mainnet/](https://blockscout.com/xdai/mainnet/)
* `status` - Boolean. Result of the payment. `true` means the payment was successful, `false` — the payment was either declined by user or failure happened

Response example:

```
{
  movieId: "423557889"
  paymentId: "0x3202b4ef03ef29d87d81357e4c50105206be0396c5e5c4624d498b8cb7e2a083"
  status: true
}
```

### Utils API

```
import { utils } from '@whiterabbitjs/client';
```

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
