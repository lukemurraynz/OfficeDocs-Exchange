---
title: 'Configure internet mail flow through a subscribed Edge Transport server'
TOCTitle: Configure Internet mail flow through a subscribed Edge Transport server
ms:assetid: d12ea770-99ce-4ab4-a373-96f2554641fa
ms:mtpsurl: https://technet.microsoft.com/library/Bb738158(v=EXCHG.150)
ms:contentKeyID: 61200301
ms.reviewer: 
manager: serdars
description: How to configure internet mail flow through a subscribed Edge Transport server in Exchange
ms.topic: article
ms.author: serdars
author: msdmaguire
f1.keywords:
- NOCSH
mtps_version: v=EXCHG.150
---

# Configure Internet mail flow through a subscribed Edge Transport server

_**Applies to:** Exchange Server 2013_

To establish Internet mail through an Edge Transport server, subscribe the Edge Transport server to an Active Directory site. This automatically creates the two Send connectors required for Internet mail flow:

  - A Send connector configured to send outbound email to all Internet domains.

  - A Send connector configured to send inbound email from the Edge Transport server to an Exchange 2013 Mailbox server.

If you don't want to subscribe the Edge Transport server to an Active Directory site, it's possible to manually create the Send connectors required to establish mail flow between the Mailbox server and the Edge Transport server. For more information, see [Configure Internet mail flow through an Edge Transport server without using EdgeSync](configure-internet-mail-flow-through-an-edge-transport-server-without-using-edgesync-exchange-2013-help.md). However, we recommend you subscribe the Edge Transport server to the Active Directory site whenever possible.

## Before you begin

  - Estimated time to complete: 20 minutes.

  - You need to be assigned permissions before you can perform this procedure or procedures. To see what permissions you need, see the "EdgeSync" entry and the "Edge Transport servers" in the [Mail flow permissions](mail-flow-permissions-exchange-2013-help.md) topic.

  - Before you subscribe an Edge Transport server to your organization, you will first need to configure authoritative domains and email address policies for your Exchange organization.

  - Enable the secure LDAP port 50636/TCP through the firewall separating your perimeter network from the Exchange organization. The Edge Transport server needs to be able to communicate with all Exchange 2013 Mailbox servers in the subscribed Active Directory site.

  - For information about keyboard shortcuts that may apply to the procedures in this topic, see [Keyboard shortcuts in the Exchange admin center](keyboard-shortcuts-in-the-exchange-admin-center-2013-help.md).

> [!TIP]
> Having problems? Ask for help in the Exchange forums. Visit the forums at [Exchange Server](https://social.technet.microsoft.com/forums/office/home?category=exchangeserver).

## Configure Internet mail flow through a subscribed Edge Transport server

1. On the Edge Transport server, create the Edge Subscription file using the following syntax.

    ```powershell
    New-EdgeSubscription -FileName <FileName>.xml [-Force]
    ```

    The following example creates an Edge Subscription file named EdgeSubscriptionInfo.xml in the folder C:\\My Documents. The *Force* parameter suppresses prompts confirming commands that will be disabled and warnings that configuration data will be overwritten on the Edge Transport server.

    ```powershell
    New-EdgeSubscription -FileName "C:\My Documents\EdgeSubscriptionInfo.xml" -Force
    ```

2. Copy the resulting Edge Subscription file to a Mailbox server in the Active Directory site you're subscribing the Edge Transport server to.

3. On the Mailbox server, to import the Edge Subscription file, use the following syntax.

    ```powershell
    New-EdgeSubscription -FileData ([System.IO.File]::WriteAllBytes('<FileName>.xml', $file.FileData)) -Site <SiteName>
    ```

    This example imports the Edge Subscription file named EdgeSubscriptionInfo.xml from the folder D:\\Data, and subscribes the Edge Transport server to the Active Directory site named "Default-First-Site-Name".

    ```powershell
    New-EdgeSubscription -FileData ([System.IO.File]::WriteAllBytes('D:\Data\EdgeSubscriptionInfo.xml', $file.FileData)) -Site "Default-First-Site-Name"
    ```

    > [!NOTE]
    > You can use the <EM>CreateInternetSendConnector</EM> or <EM>CreateInboundSendConnector</EM> parameters to prevent one or both of the required Send connectors from being automatically created. For more information, see <A href="edge-subscriptions-exchange-2013-help.md">Edge Subscriptions</A>.

4. On the Mailbox server, run the following command to start the first EdgeSync synchronization.

    ```powershell
    Start-EdgeSynchronization
    ```

5. When you're finished, we strongly recommend you delete the Edge Subscription file from both the Edge Transport server and from the Mailbox server. The Edge Subscription file contains information about credentials used during the LDAP communication process.
