---
layout: post
title:  "Why Are Payment Systems Hard?"
date:   2017-03-09 09:00:00 -0000
categories: payments
---

## Introduction

This post is about how payment systems are different from most software systems
engineers and architects will encounter. In most systems, the challenges are
in getting the right API's and documentation, scalability, availability, etc.
With payment systems, however, the problems multiply and what seems at first to
be a straight forward solution ("We just store the orders and keep them
updated, right?") can be quickly overwhelmed by corner cases, error recovery,
and reporting requirements. This post will go over some of the dimensions often
overlooked by first-time payment system engineers.

The five dimensions along which payment systems differ from pretty much every
other computer system are:

  - Customer Expectations
  - Payment System Complexity
  - Regulatory Requirements
  - Accounting and Reporting Requirements
  - Foreign Exchange Requirements

This is not exhaustive, but cover the high points. Each of these sections
could be expanded into a separate article which is something I may do in the
future.

I revised this article to add in a couple of sections on taxation and PCI
compliance.

## Customer Expectations

"My post is gone!" "My app just died!" "My picture got posted twice!" such
complaints are myriad and plague most of the software that is out there. The
total amount of angst and anger directed at developers over such bugs is large.
But the magnitude and the intensity of such anger are orders of magnitude less
then that created when payments are lost, someone is double charged, refunds
are late etc.

The irritation of someone who has lost their text or a click is but a droplet in
the raging maelstrom of rage of someone who has been charged twice for an item
he/she has not (or cannot) receive.  Consumer's tolerance for errors and
mistakes in payment systems should be considered close to nil. As such, the
time needed to respond is less, the accuracy and correctness of code needs to
be much higher, and the penalties for failure is much higher still.

It is an order of magnitude harder to get payment systems to work as well as
other systems. Yet they need to work better because of the expectations of all
parties involved. When things go well it's "you're just doing your job". And
when things go badly (as they invariably do, even in the best of systems), the
amount of shit dumped on the developers is an order of magnitude higher. That's
the reason that more than 90% of the developers I know who have worked in
payments never go back. The risk/reward just isn't there: you get paid about
the same for work that is harder, appreciation for success is about the same,
and the penalty for failure is higher.

## Payment System Complexity

Most API's used throughout the industry were created in the last decade or so
and seldom exceed a few dozen calls and return conditions. The payments industry
API's have been built up over decades and is many (many) layers deep. And
unlike most API's they do not involve a single party, there are often 4 or 5
legal entities involved in a single Credit Card (CC) AUTH request.

  - Buyer: The person buying the item with the CC
  - Issuing Bank: The bank which issued the CC
  - Network: Visa or MC or other Association
  - Acquiring Bank: The bank which set up the merchant
  - Seller: The merchant selling the item

Most non-payment systems involve a single party (or at most two parties) and
yet the myriad of failure, recovery, verification, and validation pathways can
become overwhelming if not managed properly. Now imagine multiplying the number
of entities by 2 or 3. Add to that transactions are initially completed
synchronously but stretch over time with potential future actions (chargeback?)
for each transaction.

And this system wasn't built in the last decade. No, this system was built up
over the last *century*. This is why most payment systems have response code
listings that number in the hundreds.

Some things often overlooked by new payments developers are:

  - Did you know that you can get a successful but partial auth?
  - Should you/can you split payment methods?
  - What's the difference between void vs refund vs reversal?
  - What happens in your system with multiple partial refunds?
  - How should you account for chargebacks occurring after settlement? After closing?

## Regulatory Requirements

This section will focus on U.S. laws and regulations, but most nations will
have a comparable degree of complexity. Here's a partial list of a few
regulations which govern payments for U.S. companies:

[Office of Foreign Asset Control
(OFAC)](https://en.wikipedia.org/wiki/Office_of_Foreign_Assets_Control) is the
general term for what is actually a whole host of agencies, laws, regulations,
and executive orders which prohibit economic interaction with specific entities
and countries. It (mostly) boils down to a list of individuals, organizations,
and countries with whom U.S. corporations are prohibited from doing business.
Failing to comply with OFAC lists can result in fines, freezing of assets, and
significant federal penalties. Never heard of OFAC (as in "Oh Fack, we forgot
to restrict buyers from North Korea?!")? I guarantee that your payment
processor has.

[The Sarbanes-Oxley Act](https://en.wikipedia.org/wiki/Sarbanes–Oxley_Act)
often reduced to "sox". Most of the requirements around SOX compliance for
programmers centers around record keeping, accountability, and limiting access.
The requirements around reporting and accountability put very specific
limitations on how records can be altered and the amount of access developers
can have over production payment systems.  A most basic example is that
auditors will frown upon software engineers who write the software having
control of production systems. 

[Generally Accepted Accounting Principles aka
GAAP](https://en.wikipedia.org/wiki/Generally_accepted_accounting_principles).
For developers, this mostly boils down to being able to answer the questions:
"where is this money?" and "who owns this money?" This is actually good place
to segue into my next section.

[PCI DSS compliance](https://www.pcisecuritystandards.org). In a rare moment of
cooperation, the major card companies got together to form the Payment Card
Industry organization which maintains and publishes the Data Security
Standards. They specify standards for the storage, transmission, and other
handling which need to be followed when dealing with payments data. If you are
storing any payments related data, you need to be aware and (at a minimum) will
need to do a self assessment of your set up.

Sales and VAT Tax. If you are selling any kinds of goods or services, you need
to be aware of your tax collecting and remittance obligations. Also,
regulations with regard to how taxes need to be displayed in receipts will vary
by location. Also, depending on the goods you are selling, you many need to
verify and manage sales and VAT tax exemption numbers. 

## Reporting, Accounting, and Finance

Accounting and finance are often spoken together but they are actually separate
functions. Most of the interaction of programmers with your accounting and
finance departments will be through reporting. It would be impossible to delve
into the even the most basic overview of the full practice of accounting and
finance so I will just touch on some of the complexities in reporting which are
often overlooked by newly minted payments engineers.

Let's say that your company W sells widgets. You have a website that let's
people select the number of widgets to buy and they (virtually) swipe a CC to
buy said widgets which you then ship from a warehouse somewhere. Here is the
basic sequence of events around a single purchase and partial return (note to
experts out there, I know that what follows simplifies and glosses over some of
the steps):

  1.  Buyer creates a cart with four widgets for a total of $20
  1.  Buyer enters CC information which is used to *authorize* $20
  1.  Warehouse ships 4 widgets to user
  1.  Company W *captures* the $20 based on the original authorization
  1.  Company receives *settlement* of transaction of +$20 (in reality less any
  concurrent fees charged by the network, acquiring bank etc.)
  1.  Buyer decides he only needs two widgets and requests an RMA (return
  merchandise authorization)
  1.  Company issues an RMA to Buyer
  1.  Buyer ships two widgets back to Company
  1.  Company W receives the two widgets
  1.  Company issues a refund of $10 to the Buyer (hopefully referencing the original transaction)
  1.  $10 from Company's account are moved out.
  1.  $10 appear in Buyer's account.

Now at each step of that process after the first, there is a recorded CC
transaction and monies which exist in some bank account somewhere. And at each
step of the way, the location and/or owner of those monies changes. Until the
warehouse ships, the $20 is still in the buyer's bank account, but has been
reserved by the authorization request and cannot be spent. However, as the item
has still not shipped, the legal owner of the money is still the buyer. After
the company ships the items and issues the capture request, the funds are not
in the buyer's account, but have not yet appeared in the company's account.
Yet, the $20 must be registered as belonging to the company (and entered as
revenue for that day/week/month/quarter). Once the buyer requests an RMA,
however, $10 of that revenue needs to be unrecognized from that same time
period (ever wonder why financial statements for months/quarters don't close
the same day the quarters end?). And, of course, the money doesn't instantly
appear in the buyer's account but there is another round of money movement in
the real world.

Now multiply those states and steps for every transaction you do. Then add in a
host of off-the-beaten-path issues like: RMA's issued but items not received,
items shipped but not received, partially fulfilled orders, chargebacks 29 days
after the original payment etc. etc. and you should be able to see how the
question of "How much money did we make last month?" can become a complicated
reporting problem.

## Foreign Exchange (FX) Fun

Are we having fun yet? Now take the (relatively) straightforward example from
above, and add in a different currency where the user is paying with a GBP based CC
but is being authorized and captured in USD. When we do the authorization for
$20 dollars the buyer is charged around 17 GBP. However due to the variable
nature of FX, when we issue the refund for $10 USD, the buyer gets refunded 7
GBP instead of 8.5 like he's expecting. He naturally calls and complains. Did
you track which FX rate was used for which part of the transaction?

Or just as much fun, if you have USD and GBP prices and are settling in USD,
under which ledger do you put the excess/shortfall which happens due to changes
in FX?

## Conclusion

If you made it this far, I salute you! You should consider a career in payments
engineering. If your company/organization/engineers are struggling with the
above complexity, I can help you get clarity about the workflows and paradigms
which, although they can't eliminate the complexity of payments, can at least
make them manageable. Send your thoughts, feedback, interest by emailing me at <a href="mailto:dlee@lucid-operations.com">dlee@lucid-operations.com</a> or 
<a href="https://twitter.com/intent/tweet?screen_name=LucidOperations" class="twitter-mention-button" data-show-count="false">tweet me @LucidOperations</a><script async src="//platform.twitter.com/widgets.js" charset="utf-8"></script>
