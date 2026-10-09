# On-sale monitor

A live view for Etainement's managers of what every buyer was doing on Ticketmaster during an on-sale: who was in which queue, what they had carted, what failed, and what was bought. Built alone (2023 to 2026). This repository is a write-up; the code is the company's. Case study: [scottbarclay.dev/work/on-sale-monitor](https://scottbarclay.dev/work/on-sale-monitor).

## What it did

The [buyer extension](https://github.com/sbrcly/buyer-extension-writeup) records each step a buyer takes on Ticketmaster: the event page loading, the sign-in, the position in the waiting room, a cart attempt, a checkout error, an order confirmation. A copy of every record lands as a document in Firestore. The monitor signs a viewer in, subscribes to one collection at a time, and renders the newest documents as rows, one tab per record type.

Nothing is polled and nothing is written back. A row appears the moment its document exists. A keyword filter and a pause button are the only controls, because during a sale a manager has no attention to spare.

## The hard part

Ticketmaster puts buyers in a queue before a sale and reports their position every few seconds; the extension forwards every report, so each open tab produces a stream of position records. The monitor turns that stream into one answer: who is in which queue right now, and how well placed are they?

- Group records by buyer, account, and event; keep only the newest.
- Count a group as active if it reported in the last three minutes.
- Track count, lowest, highest, and average position over the last thirty.
- Keep the lowest number of users ahead seen within the hour, because on-sales start on the hour and that is the number managers ask for.

In the 2026 rewrite this logic was pulled into pure functions so it could be reasoned about on its own. Its known limit: the grouping key leaves out the browser tab, so one buyer with two tabs on one account collapses into a single queue entry.

Stack: TypeScript, React, Firestore.
