<?xml version="1.0" encoding="UTF-8"?>
  <?xml-stylesheet type='text/xsl' href='rfc2629.xslt' ?>

<!DOCTYPE rfc SYSTEM "rfc2629.dtd" [
]>

<rfc ipr="trust200902" docName="draft-pelle-opentransact-core-02"
     category="info" >

<?rfc toc="yes"?>
<?rfc sortrefs="yes"?>
<?rfc symrefs="yes" ?>

  <front>
    <title abbrev="opentransact">
      OpenTransact Core
    </title>
    <author initials="P" surname="Braendgaard" fullname="Pelle Braendgaard">
      <organization>PicoMoney Inc</organization>
      <address>
        <email>pelle@picomoney.com</email>
      </address>
    </author>
    <author initials="N" surname="Matake" fullname="Nov Matake">
      <organization>Cerego Japan Inc</organization>
      <address>
        <email>nov@matake.jp</email>
      </address>
    </author>
    <author initials="T" surname="Brown" fullname="Tom Brown">
      <address>
        <email>herestomwiththeweather@gmail.com</email>
      </address>
    </author>
    <author initials="D" surname="Nicol" fullname="David Nicol">
      <organization>TipJar</organization>
      <address>
        <email>davidnicol@gmail.com</email>
      </address>
    </author>

    <date year="2012" month="January" day="10"/>

    <area>General</area>
    <workgroup>OpenTransact</workgroup>
    <keyword>Internet-Draft</keyword>
    <abstract>
      <t>
      This document specifies the OpenTransact standard for requesting and performing transfers of assets over the http protocol.
      </t>
    </abstract>
  </front>

  <middle>

{:/nomarkdown}

# Status of Document

This document is in draft and reflects the current designs from the OpenTransact mailing list.

# Introduction

The goal is to develop a very simple low level standard for transferring an amount of an asset from one account to another.

Most payment systems and existing standards use the messaging paradigm for historical reasons. OpenTransact specifically rejects the message paradigm and prefers the RESTful (REpresentational State Transfer) resource approach as used on the web with URL's and HTTP at it's core.

We aim to create a new standard from scratch ignoring all legacy systems, while leaving it flexible enough to allow applications built on top of it to deal with legacy systems.

The standard is designed to follow standard RESTful practices and be concise and human readable.

## Assets

Within OpenTransact we use the accounting definition of the word "Asset" to mean anything fungible about which one could make an accounting book entry.

For example:

- money
- shares
- bonds
- mobile phone minutes
- hours of work
- property
- domain names
- tons of sand
- general admission tickets to an event
- et cetera

### Asset Service

An Asset Service is a service maintained by an organization to manage accounts of one asset. For money and other financial assets the Asset Service would normally be run by a Financial Service Provider of some type. Other types of assets are offered by non-financial services. The regulatory definition of "financial" is of course out of the scope of this document.

### Transaction URL

Each Asset Service has a unique transaction URL. An informal convention is developing, but is out of the cope of this document. Guidance concerning the form of these URLs with regard to specific details like currency, card type, size, color MAY become available in a future Best Current Practices document.

The service available at the transaction URL MUST follow basic REST practices:

- A transaction URL such as https://paymentsarewe.example.com/prwpoints MUST NOT contain query or fragment part.
- Loading the URL into a normal web browser should present a human readable description of the asset.
- A POST to the URL is used for creating a transaction transferring value.
- A GET to the URL from a normal web browser with specific parameters becomes a payment request that the user can authorize.
- A GET to the URL in a machine readable form such as json returns meta data about the asset and optionally a list of transactions that the current user is allowed to see.
- Each transaction has a unique URL. This SHOULD be formed by appending a unique transaction identifier to the transaction URL, such as https://paymentsarewe.example.com/prwpoints/aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d.

### Example of Asset Services

Lets say it's an imaginary electronic currency asset provider eepay.example.net they only offer one asset type, their currency. So they would only have one transaction url:

* http://eepay.example.net/transactions

If the asset provider offered multiple currencies it should have a url for each currency as they are really separate asset types:

* http://eepay.example.net/transactions/usd
* http://eepay.example.net/transactions/euro

A bank offering OT as an alternative to ACH service could have a tranaction URL for each of the currencies in which it offers checking accounts. The details of interbank settlements are out of the scope of this document.

* http://coolbank.example.com/current
* http://coolbank.example.com/savings
* http://coolbank.example.com/bonds/mortgage

A mutual fund company would have a url for each of their funds.

* http://fidelify.example.com/funds/sp500
* http://fidelify.example.com/funds/emergingmarkets

With relaxation of current regulations on brokerage transactions all going through markets, a broker (rather, a stock market) could implement an OpenTransact API and have a different URL for each symbol.

* http://nyex.example.com/trade/AAPL
* http://nyex.example.com/trade/EBAY

This would ease the barrier to registered securities being used directly as money.

If we let the URL do the describing, there are many different possibilities. This allows support for all manners of asset types.

All the above examples are fungible assets. In general it is best practice that one item of value for one asset is fungible for one another.

For unique items such as domain names, property titles and diamonds that are unique and infungible, we can still create asset urls for each item, but only accept transfer amounts of 1.

All the above examples are fungible assets.

For unique items such as domain names, property titles and diamonds that are unique and infungible, we may define a separate currency for the item representing partial ownership. The legal framework for defining the resulting partnerships and their governance is out of scope of this document.

### Derivative Assets

A Derivative asset is an asset that is based on the current asset but provides different semantics and business rules.

Examples:

- Reserves/Escrows
- Subscription services providing recurring billing
- Exchanges providing exchange of asset with another asset

It is out of scope to define here exactly how these would work, but the OpenTransact community will build a list of recipes for how to implement these and may publish further drafts outlining specifics in the future.

## Roles

OpenTransact defines 4 roles:

- Asset provider
	- The entity providing or issuing the asset
- Transferer
	- The entity transfering an amount of the asset
- Transferee
	- The entity receiving an amount of the asset
- 3rd party application
	- An application performing a transfer on behalf of the Transferer

## Transfer

We will use the term "Transfer" as it is more widely applicable than "Payment".

A Transfer is legally a transfer in ownership of some amount of the Asset from the Transferer to the Transferee.

Eg. A Payment of $12 from Bob to Alice is a Transfer of $12 with Bob being the Transferer and Alice the Transferee.

## Transfer Request

A Transfer request is when the Transferee requests a transfer of a given amount of an asset from the Transferer.

Eg. Alice sends an invoice to Bob for $12. This is a Transfer Request from Alice to Bob where Alice is the Transferee and Bob the Transferer.

A Transfer Request is simply a specially formatted payment link that takes Bob to Asset Service if followed. The Asset Service shall present the Transfer Request to Bob who can authorize or decline it.

## Transfer Authorization

A Transfer Authorization is when  the Transferee or a third party application requests an authorization to transfer of a given amount of an asset from the Transferer.

Eg. Bob wants to hire someone on an online job board to edit a document for $33. The Job Board creates a Transfer Authorization link. Bob follows this link to the Asset Service and authorizes the Job Board to transfer $33 from his account to some one else within a time period.

A Transfer Request is simply a specially formatted payment link that takes Bob to Asset Service if followed. The Asset Service shall present the Transfer Request to Bob who can authorize or decline it. If Bob authorizes it the Asset Service issues an authorization code to the Job Board that they can use to exchange for an OAuth token.

## Exchange Transaction

A Transfer is often but not always part of an exchange of value between 2 types of assets. Eg.:

- 10 consulting hours exchanged for 1100EUR
- 10 USD exchanged for 8 EURO
- 0.99 USD exchanged for an mp3 song

There are as many different exchange mechanisms for creating exchanges as there are exchange types.

- Invoicing system
- App Store
- Web shop
- Auction site
- Stock Exchange

It is outside the scope for OpenTransact to define every single type of exchange that is possible. OpenTransact provides a fundamental building block in building such systems. It integrates well with exchange systems that don't yet understand OpenTransact.

### Exchanged item URI

In an Exchange Transaction we can include a url to the exchanged item. This URI could either be a link to the exchanged item itself or the unique URI identifying the transaction itself.

### Authorization

A useful building block for creating Exchange services is the Transfer Authorization flow.

## Vocabulary

OpenTransact includes a standard list parameter names used in the GET and POST requests.

- *asset* - Transaction URL
- *from* - Transferer account identifier
- *to* - Transferee account identifier
- *amount* - Amount of asset
- *note* - Textual description of transfer
- *for* - Identifier of exchanged item. This SHOULD be a URI
- *redirect_uri* - URI to redirect User Agent to
- *callback_uri* - URI for performing callback after request
- *client_id* - Identifier of 3rd party application
- *expires_in* - Request that a token expires in given seconds

They are designed to be small and semantically correct. eg

> A transfer of 10  USD  from Bob to Alice for consulting.

Would become the following:

- amount=10
- asset=http://pay.me/usd
- from=bob@test.example..com
- to=alice@test.example..com
- note=Consulting on XYZ project
- for=http://myinvoice.test/invoices/123123

## Authentication

OpenTransact does not define any new authentication mechanisms, but relies on the Asset Provider's existing mechanisms for authenticating the Transferer and  OAuth 2.0 {{I-D.ietf-oauth-v2-bearer}} for authenticating 3rd party applications on behalf of the Transferer.

## Parameter encoding

Since OpenTransact is designed to be simple to implement, the basic parameter encoding is URL form encoding.

Data responses should be in JSON format.

OpenTransact includes a standard for name strings and value ranges for use with the JSON objects returned from a request made to a compliant OT transaction URL.

## Extensions

OpenTransact is designed to be extensible, either through proprietary extensions, conventions or futher standards.

For example it may be useful to follow the lat/lon convention allowing geotagging of data.


## Notational Conventions

The key words 'MUST', 'MUST NOT', 'REQUIRED', 'SHALL', 'SHALL NOT', 'SHOULD', 'SHOULD NOT', 'RECOMMENDED', 'MAY', and 'OPTIONAL' in this specification are to be interpreted as described in {{RFC2119}}.

This specification uses the Augmented Backus-Naur Form (ABNF) notation of {{RFC5234}}.

Certain security-related terms are to be understood in the sense defined in {{RFC4949}}. These terms include, but are not limited to, 'attack', 'authentication', 'authorization', 'certificate', 'confidentiality', 'credential', 'encryption', 'identity', 'sign', 'signature', 'trust', 'validate', and 'verify'.

Unless otherwise noted, all the protocol parameter names and values are case sensitive.

# Transfer Request

A Transfer Request consists of a GET to the Asset URL.

    GET /usd?to=bill@example.com&amount=100.00&note=Milk&redirect_uri=http://site.example.com/callback HTTP/1.1
    Host: pay.me

We use the following parameters from our common vocabulary. All fields are optional:

- *to* Account identifier of Transferee. If left out it defaults to the 3rd party applications own account on Asset Service or a predefined account as specified when authorizing the access token.
- *amount* Amount as a number with decimal points. Symbols are allowed but SHOULD be ignored. If left out it defaults to the Asset's minimum transfer, 1 or an amount predefined when authorizing the access token.
- *note* Textual description, which can include hash tags. Asset Service may truncate this. No default.
- *from* Account identifier of Transferer. This should normally be left out as it is implied by the authorizer of the Access Token. The Asset Service MUST verify that the Access Token is authorized to transfer from this account. This could be useful for Asset providers charging their customers accounts.
- *for* URI identifying the exchanged item.
- *redirect_uri* URI for redirecting client to afterwards
- *callback_uri* URI for performing a web callback

When a user follows this link, the Asset Service should present the user with a form authorizing the payment.

Note: Client can include OpenID Connect parameters.

## Response

The user is redirected back to the clients redirect uri with the following url encoded parameters:

- *txn_url* A url identifying the transaction.

Asset provider can include an access_token in the query string of txn_url.

## Errors

Error types use OAuth 2.0's error codes. {{I-D.ietf-oauth-v2}}

# Transfer Authorization

A Transfer Authorization consists of a GET to the Asset URL with a client_id.

    GET /usd?to=bill@example.com&amount=100.00&note=Milk&redirect_uri=http://site.example.com/callback&client_id=1234 HTTP/1.1
    Host: pay.me

A Transfer Authorization is really a OAuth2 Authorization {{I-D.ietf-oauth-v2}} with a few extra payment related parameters.

We use the following parameters from our common vocabulary. All fields are optional:

- *to* Account identifier of Transferee. If left out it defaults to the 3rd party applications own account on Asset Service or a predefined account as specified when authorizing the access token.
- *amount* Amount as a number with decimal points. Symbols are allowed but SHOULD be ignored. If left out it defaults to the Asset's minimum transfer, 1 or an amount predefined when authorizing the access token.
- *note* Textual description, which can include hash tags. Asset Service may truncate this. No default.
- *from* Account identifier of Transferer. This should normally be left out as it is implied by the authorizer of the Access Token. The Asset Service MUST verify that the Access Token is authorized to transfer from this account. This could be useful for Asset providers charging their customers accounts.
- *for* URI identifying the exchanged item.
- *validity* A {{ISO.8601.1988}} [duration](http://en.wikipedia.org/wiki/ISO_8601#Durations) or [interval](http://en.wikipedia.org/wiki/ISO_8601#Time_intervals) (see below)

OAuth2 related parameters. See {{I-D.ietf-oauth-v2}} section 5 for full details

- *client_id* OAuth2 client id
- *redirect_uri* URI for redirecting client to afterwards
- *callback_uri* URI for performing a web callback
- *response_type* token or code REQUIRED

When a user follows this link, the Asset Service should present the user with a form authorizing the payment.

Note: Client can include OpenID Connect parameters.

## Validity duration

A client can request a specific validity length of the oauth token.

The asset provider will assign a default expiration of the token if client doesn't specify a validity.

A validity can be request which is either a fixed duration  or a repeating interval.

For a fixed interval the amount SHOULD be guaranteed and reserved for the client.

For a repeating interval the amount SHOULD be guaranteed for the first interval only.

### Validity Request Parameter

The validity is requested using A ISO8601 [duration](http://en.wikipedia.org/wiki/ISO_8601#Durations),  [interval](http://en.wikipedia.org/wiki/ISO_8601#Time_intervals) or [repeating interval](http://en.wikipedia.org/wiki/ISO_8601#Repeating_intervals).

A duration is written

    P30D                      # 30 days
    PT5M                     # 5 minutes
    P1M14DT3H5M23S # 1 month 14 days 3 hours 5 minutes and 23 seconds

Intervals are either 2 ISO Dates or an ISO date and a duration separated by the '/' character:

    2007-03-01T13:00:00Z/2008-05-11T15:30:00Z # The interval between 2 dates
    2007-03-01T13:00:00Z/P1Y2M10DT2H30M     # The interval starting at a date and finishing after a duration
    P1Y2M10DT2H30M/2008-05-11T15:30:00Z     # The interval starting with a duration and finishing at a given date
    P1M                                                             # The duration starting at the time the token was authorized.

Repeating intervals by prefix one of the above intervals or durations with the letter 'R' and an optional number specifying the amount of times to repeat:

    RP1M                     # repeat once a month
    R12P1M                 # repeat 12 times once a month
    RP1M/2008-05-11T15:30:00Z  # repeat monthly until a given date

By supporting a single parameter with any of the above durations/intervals/repeated intervals we can support a lot of different kinds of applications:

- Recurring payments (subscriptions) using the Repeated Intervals
- Daily spending limits on tokens using Repeated intervals similar to what debit cards have to day. eg. $300/day
- Request validity of a token using a simple duration

### Validity Request Error

If a specified interval is not supported by service or user refuses to authorize the interval an [OAuth2.0 Authorization error response](http://tools.ietf.org/html/draft-ietf-oauth-v2-22#section-4.1.2.1) MUST be returned through redirection with the following error:

  error=unsupported_interval


## Response

Follows OAuth 2 response depending on response_type requested. {{I-D.ietf-oauth-v2}}

## Errors

Error types use OAuth 2.0's error codes. {{I-D.ietf-oauth-v2}}

# Transfer

A transfer consists of a HTTP POST to the asset url by a 3rd party application on behalf of the Transferer.

The Application MUST have an OAuth 2.0 access token issued as defined in the {{I-D.ietf-oauth-v2}} section 7.

The asset provider SHOULD support the {{I-D.ietf-oauth-v2-bearer}} Access Token type and can support other access token such as {{I-D.ietf-oauth-v2-http-mac}}.

The Transfer MUST be created using HTTPS when using {{I-D.ietf-oauth-v2-bearer}} and other unsigned access tokens.

    POST /usd HTTP/1.1
    Host: pay.me
    Authorization: Bearer vF9dft4qmT

    to=bill@example.com&amount=100.00&note=Milk

We use the following parameters from our common vocabulary in 1.6. All fields are optional:

- *to* Account identifier of Transferee. If left out it defaults to the 3rd party applications own account on Asset Service or a predefined account as specified when authorizing the access token.
- *amount* Amount as a number with decimal points. Symbols are allowed but SHOULD be ignored. If left out it defaults to the Asset's minimum transfer, 1 or an amount predefined when authorizing the access token.
- *note* Textual description, which can include hash tags. Asset Service may truncate this. No default.
- *from* Account identifier of Transferer. This should normally be left out as it is implied by the authorizer of the Access Token. The Asset Service MUST verify that the Access Token is authorized to transfer from this account. This could be useful for Asset providers charging their customers accounts.
- *for* URI identifying the exchanged item.

## Response

http 201 with Receipt json.

# Receipt

The receipt is returned when creating a transaction as well as when accessing a transaction url. It can also be used for creating a transaction list by the asset provider.

The receipt is a JSON object consisting of the following fields:

- *txn_url*
- *to*
- *from*
- *amount*
- *note*
- *for*
- *asset_url*
- *timestamp*


# Asset Meta data

A client can find out information about an asset by accessing the asset url directly with a http Accept header of application/json:

    GET /usd HTTP/1.1
    Host: pay.me
    Accept: application/json

This returns a json hash of meta information about the asset.

The minimum required data would be:

- name - Short name of asset

The minimal asset meta data is:

    {
      "name":"Pay Me"
    }

Further OpenTransact specific parameters could be:

- default_amount - The default amount transfered if an amount is not specified in a transfer
- provider_uri - The provider of the asset's home page
- description - Short description
- logo_uri - Image url for Assets logo
- unit - ISO currency unit of asset if monetary or other such as (minute, gram, point etc)
- derivatives - a list of derivative assets supported by this server (see below)

Example:

    {
      "name":"Pay Me",
      "default_amount":1.0,
      "provider_uri":"http://pay.sample.com",
      "logo_uri":"http://pay.sample.com/logo.png",
      "unit":"USD",
      "derivatives": [
        {"reserve":"http:/pay.sample.com/reserves"},
        {"subscription":"http:/pay.sample.com/subscriptions"},
        {"exchange":"http:/pay.sample.com/exchange"}
      ]
    }

Asset services can provide further information more specific to their particular asset type.

If an OAuth Access Token is provided the Asset Service should provide information related to the capabilities of the token.

- balance - balance of account
- available_balance - available balance of account

For tokens obtained through a Transfer Authentication this should reflect the remaining balance of the authorized amount.

## Transaction history

If tokens scope allows access to accounts transaction history a url to the transaction history or a transaction history SHOULD be included here:

- transactions_uri - URI to list of transactions
- transactions - array of receipts

## Derivative assets

If an asset has derivative assets they SHOULD be listed in the optional derivatives list:

    {
      "name": "Pay Me",
      "derivatives": [
        {"reserve":"http:/pay.sample.com/reserves"},
        {"subscription":"http:/pay.sample.com/subscriptions"},
        {"exchange":"http:/pay.sample.com/exchange"}
      ]
    }

The OpenTransact group will maintain a list of recipes that may be further standardized.

# Security Considerations

The security of OpenTransact is achieved by it's use of {{I-D.ietf-oauth-v2}}. Please see its [security discussion](http://tools.ietf.org/html/draft-ietf-oauth-v2-22#section-10) and a more thorough discussion in {{I-D.ietf-oauth-v2-threatmodel}}.

{::nomarkdown}
</middle>

<back>

  <references title='Normative References'>

{:/nomarkdown}
![:include:](RFC2119)
![:include:](RFC2616)
![:include:](RFC3339)
![:include:](RFC4627)
![:include:](RFC5234)
![:include:](RFC4949)

<?rfc include='http://xml.resource.org/public/rfc/bibxml3/reference.I-D.draft-ietf-oauth-v2-22.xml' ?>
<?rfc include='http://xml.resource.org/public/rfc/bibxml3/reference.I-D.draft-ietf-oauth-v2-bearer-15.xml' ?>
<?rfc include='http://xml.resource.org/public/rfc/bibxml3/reference.I-D.draft-ietf-oauth-v2-http-mac-00' ?>
<?rfc include='http://xml.resource.org/public/rfc/bibxml3/reference.I-D.draft-ietf-oauth-v2-threatmodel-01.xml' ?>
<?rfc include='http://xml.resource.org/public/rfc/bibxml2/_reference.ISO.8601.1988.xml' ?>

{::nomarkdown}

  </references>

</back>
</rfc>
