---
ms.localizationpriority: medium
description: 'Summary: Learn how retention age is calculated in Exchange Server 2016 and Exchange Server 2019.'
ms.topic: article
author: JoanneHendrickson
ms.author: jhendr
ms.assetid: a7daf7aa-0411-4b26-a422-eefd1b113f9f
ms.reviewer:
title: How retention age is calculated in Exchange Server
ms.collection: exchange-server
f1.keywords:
- NOCSH
audience: ITPro
ms.prod: exchange-server-it-pro
manager: serdars

---

# How retention age is calculated in Exchange Server

The Managed Folder Assistant (MFA) is one of many mailbox assistant processes that runs on mailbox servers. Its job is to process mailboxes that have a Retention Policy applied, add the Retention Tags included in the policy to the mailbox, and process items in the mailbox. If the items have a retention tag, the assistant tests the age of those items. If an item has exceeded its retention age, it takes the specified retention action. Retention actions include moving an item to the user's archive, deleting the item and allowing recovery, or deleting the item permanently.

See [Retention tags and retention policies in Exchange Server](retention-tags-and-retention-policies.md) for more information.

## Determining the age of different types of items

The retention age of mailbox items is calculated from the date of delivery or the date of creation for items such as drafts that are not delivered but created by the user. When the Managed Folder Assistant processes items in a mailbox, it stamps a start date and an expiration date for all items that have retention tags with the **Delete and Allow Recovery** or **Permanently Delete** retention action. Items that have an archive tag are also stamped with a move date.

Items in the Deleted Items folder and items which may have a start and end date, such as calendar items (meetings and appointments) and tasks, are handled differently as shown in this table.

|If the item type is...|And the item is...|The retention age is calculated based on...|
|---|---|---|
|Email message <br/><br/> Document <br/><br/> Fax <br/><br/> Journal item <br/><br/> Meeting request, response, or cancellation <br/><br/> Missed call <br/><br/> Notes|Not in the Deleted Items folder|Delivery date or date of creation|
|Email message <br/><br/> Document <br/><br/> Fax <br/><br/> Journal item <br/><br/> Meeting request, response, or cancellation <br/><br/> Missed call <br/><br/> Notes|In the Deleted Items folder|Date of delivery or creation unless the item was deleted from a folder that does not have an inherited or implicit retention tag. <br/><br/> If an item is in a folder that doesn't have an inherited or implicit retention tag applied, the item isn't processed by the MFA and therefore doesn't have a start date stamped by it. When the user deletes such an item, and the MFA processes it for the first time in the Deleted Items folder, it stamps the current date as the start date.|
|Calendar|Not in the Deleted Items folder|Non-recurring calendar items expire according to their end date. <br/><br/> Recurring calendar items expire according to the end date of their last occurrence. Recurring calendar items with no end date don't expire.|
|Calendar|In the Deleted Items folder|A calendar item expires according to its `message-received date`, if one exists. <br/><br/> If a calendar item doesn't have a `message-received date`, it expires according to its `message-creation date`. <br/><br/> If a calendar item has neither a `message-received date` nor a `message-creation date`, it doesn't expire.|
|Task|Not in the Deleted Items folder|Non-recurring tasks: <ul><li>A non-recurring task expires according to its `message-received date`, if one exists.</li><li>If a non-recurring task doesn't have a `message-received date`, it expires according to its `message-creation date`.</li><li>If a non-recurring task has neither a `message-received date` nor a `message-creation date`, it doesn't expire.</li></ul> <br/><br/> A recurring task expires according to the `end date` of its last occurrence. If a recurring task doesn't have an `end date`, it doesn't expire. <br/><br/> A regenerating task (which is a recurring task that regenerates a specified time after the preceding instance of the task is completed) doesn't expire.|
|Task|In the Deleted Items folder|A task expires according to its `message-received date`, if one exists. <br/><br/> If a task doesn't have a `message-received date`, it expires according to its `message-creation date`. <br/><br/> If a task has neither a `message-received date` nor a `message-creation date`, it doesn't expire.|
|Contact|In any folder|Contacts aren't stamped with a start date or an expiration date, so they're skipped by the Managed Folder Assistant and don't expire.|
|Corrupted|In any folder|Corrupted items are skipped by the Managed Folder Assistant and don't expire.|

## Examples

|If the user..|The retention tags on folder...|The Managed Folder Assistant...|
|---|---|---|
|Receives a message in the Inbox on 01/26/2016. <br/><br/> Deletes the message on 2/27/2016.|Inbox: Delete in 365 days <br/><br/> Deleted Items: Delete in 30 days|Processes the message in the Inbox on 1/26/2016, stamps it with a start date of 01/26/2016 and an expiration date of 01/26/2017. <br/><br/> Processes the message again in the Deleted Items folder on 2/27/2016. It recalculates the expiration date based on the same start date (01/26/2016). <br/><br/> Because the item is older than 30 days, it is expired immediately.|
|Receives a message in the Inbox on 01/26/2016. <br/><br/> Deletes the message on 2/27/2016.|Inbox: None (inherited or implicit) <br/><br/> Deleted Items: Delete in 30 days|Processes the message in the Deleted Items folder on 02/27/2016 and determines the item doesn't have a start date. It stamps the current date as the start date, and 03/27/2016 as the expiration date. <br/><br/> The item is expired on 3/27/2016, which is 30 days after the user deleted or moved it to the Deleted Items folder.|

## More information

Items in mailboxes placed on Retention Hold aren't removed until the hold is removed.

If a mailbox is placed on In-Place Hold or Litigation Hold, expiring items are removed from the Inbox but preserved in the Recoverable Items folder until the mailbox is removed from [In-Place Hold and Litigation Hold in Exchange Server](../../policy-and-compliance/holds/holds.md).

In hybrid deployments, the same retention tags and retention policies must exist in your on-premises and Exchange Online organizations in order to consistently move and expire items across both organizations. See [Export and import retention tags](../../../ExchangeServer2013/export-and-import-retention-tags-exchange-2013-help.md) for more information.
