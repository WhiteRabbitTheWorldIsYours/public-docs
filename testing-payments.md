# Testing payments

## Using test environment

Initialise WR client with the `env` parameter set to `test`. E.g.:

```
import { WhiteRabbitClient } from '@whiterabbitjs/client';

const client = new WhiteRabbitClient({ env: 'test' });
```

In this environment users can use test credit cards to make deposits. Please keep deposit amount low (two digits) not to stress the test system.

## Test card details

Use one of the test card numbers when the client is running in a test mode: https://developers.circle.com/docs/test-card-numbers

For example:

```
card number: 4007400000000007
cvc: 222
expiry date: 07/25
name: John Doe
city: Berlin
country: Germany
address: Warshauer Str. 68
postal code: 10243
```
