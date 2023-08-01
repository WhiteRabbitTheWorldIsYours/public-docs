# Testing payments

## Using test environment

Initialise WR client with your test API key. It looks like `...-test-...` (e.g. kosta-test-abd1-89a8b883a1f6).

## Test card details

In test environment users can use test credit cards to make payments.

Different card numbers may be used to simulate different situations: payment failures, 3DS challenges, different countries etc. See the full list here: [https://stripe.com/docs/testing?testing-method=card-numbers](https://stripe.com/docs/testing?testing-method=card-numbers)

Example of the card number (always succeeds):

Card Number: 4242424242424242

Expiry Date: any future date

CVV: any 3 digits

