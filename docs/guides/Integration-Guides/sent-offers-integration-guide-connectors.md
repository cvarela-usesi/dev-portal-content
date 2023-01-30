---
title: "Offer Management Integration Guide"
slug: "sent-offers-integration-guide-connectors"
hidden: false
createdAt: "2020-07-31T22:14:39.890Z"
updatedAt: "2022-06-17T14:46:06.405Z"
---

Offer Management is the VTEX feature that gives sellers more visibility around a product’s sending process to external channels, like marketplaces. Offer Management does not reflect what happens to offers after they’re sent to channels and start being sold. Instead, it helps sellers identify updates and solve errors in their offers during the sending process, guaranteeing that they can be sent to the marketplace and synced correctly.

Connectors are responsible for integrating marketplaces to Offer Management, so that sellers can have updated information about their offers, and solve any errors around them. This documentation is a guide for connectors to integrate with Offer Management.

Connectors interact with Offer Management in four ways:

1. Creating channel.
2. Activating feed.
3. Updating offers, by creating interactions.
4. Registering all actions in the Offer Management timeline, by creating logs.

[block:callout]
{
  "type": "info",
  "body": "**VTEX Bridge**  was the previous VTEX module responsible for managing all marketplaces used by a seller. Offer Management replaces the product, price, and inventory features currently managed by VTEX Bridge.",
  "title": "Deprecated features"
}
[/block]

The diagram below shows the Offer Management’s interface, which the vendors access through their VTEX accounts.

![offer\_management\_ui](https://cdn.jsdelivr.net/gh/vtexdocs/dev-portal-content@main/images/sent-offers-integration-guide-connectors-0.png)

## 1.  Create Channel

> **API Reference:** Create Channel

The first step for connectors to integrate with Offer Management is to create a channel, that represents the marketplace to where sellers will send their offers.

The [Create Channel API](https://developers.vtex.com/vtex-rest-api/reference/createchannel) must be called a single time by the connector for each marketplace. The feedId created by this call will apply to all sellers connected to the given channel, and will be necessary for the next step of the integration flow.

## 2.  Feed: establishing the connection

> **API Reference:** Activate Feed

After the [channel is created](https://developers.vtex.com/vtex-rest-api/reference/createchannel), the connection between seller and marketplace can be established, through the feed. The channels available for connecting with Offer Management are:

- Mercado Livre Classic
- Mercado Livre Premium
- Netshoes
- VTEX marketplaces

The feed links the created channel to a seller’s account, so offers and their data can be exchanged. When a connector calls the [Activate Feed](https://developers.vtex.com/vtex-rest-api/reference/create-feed) API, it establishes the relation between a seller and the channel. This endpoint should only be used once, to activate the channel and establish the connection. However, If a feed is deactivated, it can be reactivated for that channel by a second call to the Activate Feed API.

### Managing the Feed ID

The `feedId` attribute that identifies a feed between a seller and a channel, follows a standardized pattern that will be used by connectors when establishing the connection between the two. This means that a feed’s  `feedId` is always the same for the relation between those specific sellers and channels, and will be used in other API calls.
Connectors can create more than one feed with a single marketplace, depending on their business rules. It doesn’t matter if a feed is established more than once, the ID is always the same, and each connector is aware of the ID implemented.

The pattern for naming the `feedId` is the following:
“seller” + “.” + “channel’s name”
Ex. “vtex.netshoes”, “anymarket.b2w”, `"vtex.meli-premium"`, `"vtex.meli-classic"`, “vtex.wish”

## 3. Interactions: where all processes happen

> **API Reference:** Open Interaction, Close Interaction

Both the Marketplace and the seller operate everything that happens to offers by creating interactions. For every action that happens to an offer, the connector must create an interaction to notify it. The image below shows how interactions appear on the Offer Management's UI.
![sent\_offers\_print\_2](https://cdn.jsdelivr.net/gh/vtexdocs/dev-portal-content@main/images/sent-offers-integration-guide-connectors-1.png)

### Interaction Lifecycle

An interaction is the space where a process around an offer and all the actions within it are recorded. It’s lifecycle is the following:

1. **Open interaction:** connectors [open an interaction](https://developers.vtex.com/vtex-rest-api/reference/open-interaction) to begin adding a process to an offer.
2. **Create logs:** while open, connectors include in that interaction all the steps that happened to make it come to life, by adding [logs](https://developers.vtex.com/vtex-rest-api/reference/create-log).
3. **Close interaction:**  the connector [closes the interaction](https://developers.vtex.com/vtex-rest-api/reference/close-interaction), once the process it was trying to accomplish is concluded and there are no more events taking place within it.

### Interaction Origins

A seller interaction’s `origin` indicates what the process  is about, and includes the following categories:

- Catalog
- Price
- Inventory

![Updates seller portal](https://cdn.jsdelivr.net/gh/vtexdocs/dev-portal-content@main/images/sent-offers-integration-guide-connectors-2.jpg)
Interactions are usually used to deal with a single type of process at a time. However, there are situations in which data about catalog, price or inventory are added in the same interaction.

> Ex: B2W’s API has only one endpoint for creating and updating products. This means that to send them a seller’s catalog, both price and inventory data must be sent through the request. So even though initially an `Inventory type`  interaction is created, price data will also be added.

### Offers Status

The seller’s goal is to send and sync all their offers on a channel. In this process, **offers** can have the following statuses:

- **Synced:**  when offers are successfully sent to a channel and are currently being synced between both entities.
- **Sending:** intermediate state, when offers are still in the process of being sent to a channel. This status involves offers that are either sent for the first time to the marketplace, migrated to a new marketplace, or resent offers that were once deleted from a channel.
- **Error:** when Offer Management finds an error that prevents sending or updating an offer to a channel. Therefore this status requires manual correction.
- **Disabled:** when the offer is discarded by the connector, by being inactive or not included in the trade policy.

### Result Status

An interaction’s goal is to generate a process, or update to an offer. Once an interaction is closed, the combination of steps that constitute it produce a `result`, that summarizes the outcome of the interaction. Interaction **results** include:

- **Success:** When events close their lifecycle successfully, and generate updates on an offer in terms of price, inventory, or catalog.
- **Failure:** When the connector has detected processes that have failed to be made  due to an error.
- **Notification:** When there are processes with the connector that are worth mentioning, but there are no actual updates.

> Ex: discarded updates or sendings of an offer.

- **Processing:**  When an open interaction hasn’t been concluded, and should still receive more steps. This is a transitory state, considering that when an interaction is closed, it can only end with `success`, `failure` or `notification` as its result.

This result is what allows Offer Management to infer an offer’s status. The types of interaction results, and their relation to offer statuses are the following:

[block:parameters]
{
  "data": {
    "h-0": "Interaction’s result",
    "h-1": "Success",
    "h-2": "Failure",
    "h-3": "Notification",
    "h-4": "Processing",
    "0-1": "**Status: Synced** \n When closing a `success` interaction, Offer Management updates the offer’s status to `Synced`, if there aren't any active errors in this offer.\n\n\n**Status: Unavailable**\n When the offer already exists and the seller removes it from the trade policy, Offer Management doesn’t infer an offer’s status, and the connector indicates it. In this case, the connector must also remove the duplicate from the channel. Once the action is done, the interaction should be a success. \n\n In cases, the connector will send the `\"status\":\"unavailable\"` data in an interaction.",
    "0-2": "**Status: Error**\nWhen an offer has at least one failure interaction, the offer’s status becomes `error`.",
    "0-3": "**Status: Unavailable**\nWhen the offer is discarded by the connector - by being inactive or not included in the trade policy, Offer Management doesn’t infer an offer’s status, and the connector indicates it.  In that case, the connector will send the `\"status\":\"unavailable\"` data in a interaction.",
    "0-4": "**Status: Sending** \nWhen the interaction is being processed, the offer’s status becomes `sending`when the interaction has the attribute “context”=”setup”.",
    "0-0": "**Corresponding status** "
  },
  "cols": 5,
  "rows": 1
}
[/block]

## 4. Logs: registering steps

> **API Reference:** Create Log

Logs are the granular details of actions that happen within an interaction, organized in a timeline. They are the way an interaction is made visible in Sent Offer’s UI. All micro steps that go through an interaction will be represented through logs.

### Log Types

Connectors are responsible for attributing the correct type for each log. Therefore, be mindful of their correct usage, by following the descriptions below:

- **Success:** only logs that conclude an interaction’s goal should be `“type”=”success”`.
  *Ex. concluded updates in price, inventory, offer ID, or catalog.*
- **Information:** logs that expose the actions of an update process, to provide visibility and contribute with more technical investigations. Info logs may include internal processes of a system (`agent` attribute), or calls between two systems.
  *Ex. Placing an offer in the queue, indicating that an offer is being processed, indicating that a call to Catalog has been made, adding requests and responses as evidences, etc.*
- **Warning:** logs used in cases where the communication between two systems fails, and there are no actions that the user can take to fix it. They are usually cases where the connector should perform a retry, and provide visibility of what is being attempted again.
  *Ex. Throttling, service unavailable, etc.*
- **Failure:** logs noticed by the connector as errors that prevent an offer from being correctly sent and synced to a channel. They will be described in more detail in the following section.
  *Ex. Product descriptions that exceed the character limit, product’s image with low quality, etc.*

## 5. Actions performed by creating interactions and logs

### Sending offers to the marketplace

> **API Reference:** Activate Feed, Open Interaction, Create Logs, Close Interaction

Connectors are the agents responsible for sending the seller’s products to their channels. They do that by creating events, since that is the mechanism for reporting all interactions around offers. Consider the following example, in which a seller wants to integrate with Mercado Livre:

1. Activate the **Feed**.
2. Open **Interaction**.
3. Create **Log**.
   *In that log, connectors indicate the series of actions that took place for the offers to be created.*
4. Call **Catalog** API.
   *One of the logs shows that connectors called the Catalog API to identify the SKUs that were present in the Mercado Livre trade policy, and try to send their offers to that channel. As long as that analysis happens, the offer’s status is `sending`.*
   *SKUs that are identified as being a part of Mercado Livre’s trade policy are sent to the channel. - All SKUs that don’t belong to that trade policy are discarded, and kept in the Discarded Offers page, in the UI.*
5. Create **Log.**
   *Once this analysis process is over, and the connector has identified which SKUs belong to the channel, and which can be discarded, the connector can create the last log of that event, indicating success or failure.*
   *It is important to point out that whenever a Catalog log is created, Offer Management evaluates if that offer already exists in its base. If it’s the first load of products, or if a new product is added, the `context` field’s value is `setup`.*
6. Close **Interaction**.

### Updating offers through Interactions

> **API Reference:**  Open Interaction, Create Logs, Close Interaction

**Updates from sellers:** Sellers notify the marketplace of all changes that come from their side, by creating interactions. These are the types of changes that come from sellers:

- Product information, from the Catalog.
- Price value.
- Inventory level.

**Updates from Marketplaces:** The Marketplace notifies Offer Management about all changes originating from its side. These are the changes that come from marketplaces:

- Changes in the Offer ID from the Marketplace’s side.
- Errors detected.

### Pointing out errors in offers

> **API Reference:** Create Log, Search Errors

It is the connector's responsibility to identify errors that prevent an offer from being sent to a  marketplace. This log allows the connector to notify the Offer Management that an error has occurred. Connectors point out problems with an offer by creating failure logs. From the information sent through the `errors` attribute, sellers can identify and fix errors on their offers.

Connectors must identify all errors, and send them all in a single request. This means that connectors should go through every possible validation, and only after all errors are identified, create the failure log.

#### Error codes

The Offer Management owns the intelligence by recognizing most of the problems that happen between VTEX, connectors and channels. This way, through a code, a connector will be able to identify pre-defined classifications and solving actions for their errors and easily leverage from it without needing development from their end. All the attributes of a code are filled and maintained by Offer Management. The Offer Management will create, update and delete codes in order to always keep an updated list for connectors leveraging from it.

The **Search Errors** endpoint returns the updated list of error codes. It is the only source of truth about the codes and where they will be always updated.

#### External code

Connectors must extend the Offer Management' codes by adding their own code IDs for mapping specific scenarios that apply to their own system. They can send those codes through a suffix ID added in an existing code. This way the Offer Management knows which errors must be archived and which errors must be kept, being able to classify that given error and improve its solving action. These suffix IDs are totally managed by the connectors, which will require them a way to map each error inside their own code.

The format for adding external codes should be:

`internalCode` + `connectorCode`

Ex: let's say the Offer Management code for errors in the description field is `CTLG-005`. On Marketplace A, it is not allowed to use HTML on the description, and to use more than 4000 characters. On Marketplace B it is not allowed to use more than 5000 characters.

So Marketplace A’s connector would insert errors of description with HTML extending the original code like this: `CTLG-005-001`. And the character one could be `CTLG-005-002`. While Marketplace B’s connector could insert `CTLG-005-001`.

#### Unmapped errors

It is expected that errors that haven’t been mapped by Offer Management, and aren’t yet on the error’s code list, may appear. Follow the practices below to identify and deal with them:

- **Unmapped error’s code:** for those scenarios, the Offer Management created the following error code: `NTMAP-001`. This code should be used by the connector when they don’t recognize the error received.
- **Identifying unmapped errors:** in this case, we recommend using the hash method to map these errors. Ideally, this method should be used when the error’s description doesn’t present variables. Thus it will be possible to monitor and verify which unmapped errors are occurring more frequently, so they are later codified. It is a cyclical process, where the connector provides new data so that the Offer Management can improve its error mapping.
- **Adding evidence to unmapped errors:** unmapped errors still need evidences in their logs so sellers understand the situation. We recommend sending the error detected by the channel’s API response in the `evidence` field.

### Updating and deleting a Feed

> **API Reference:** Update Feed, Deactivate Feed

Updating a feed’s information is only possible through API calls. Only the trade policy and affiliate ID can be changed. When a channel integration is uninstalled by the seller, the connector must delete the feed in Offer Management as well. That way, that channel won’t appear in the UI. This action is done by the Delete Feed endpoint.

Note that once the feed is deleted, all offers’ data, errors and their logs will also be deleted from the UI. However, the source of truth around the products sent to the channel will still remain in the catalog, and log’s history will be preserved. If the channel integration is reinstalled, the whole process described in this guide must begin again, done from scratch. This means the connector will have to send all offers from the catalog to the channel again anyways.

## API Reference

1. [Create Channel](https://developers.vtex.com/vtex-rest-api/reference/createchannel)
2. [Activate Feed](https://developers.vtex.com/vtex-rest-api/reference/create-feed)
3. [Update Feed](https://developers.vtex.com/vtex-rest-api/reference/update-feed)
4. [Get Feed by feedId](https://developers.vtex.com/vtex-rest-api/reference/get-feed-by-feedid)
5. [Deactivate Feed](https://developers.vtex.com/vtex-rest-api/reference/deactivate-feed)
6. [Open Interaction](https://developers.vtex.com/vtex-rest-api/reference/open-interaction)
7. [Get Interaction Data by interactionId](https://developers.vtex.com/vtex-rest-api/reference/get-interaction-data-by-interactionid)
8. [Close Interaction](https://developers.vtex.com/vtex-rest-api/reference/close-interaction)
9. [Create Log](https://developers.vtex.com/vtex-rest-api/reference/create-log)
10. [Get Log Data by logId](https://developers.vtex.com/vtex-rest-api/reference/get-log-data-by-logid)
11. [Search Interactions and their Logs](https://developers.vtex.com/vtex-rest-api/reference/search-interactions-logs)
12. [Search Errors](https://developers.vtex.com/vtex-rest-api/reference/search-errors)
13. [Get Error Code data by errorCodeId](https://developers.vtex.com/vtex-rest-api/reference/get-error-code-data)