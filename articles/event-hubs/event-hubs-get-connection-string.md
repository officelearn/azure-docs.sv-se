---
title: Hämta anslutningssträng – Azure Event Hubs | Microsoft-dokument
description: Den här artikeln innehåller instruktioner för hur du hämtar en anslutningssträng som klienter kan använda för att ansluta till Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: 77a768f907ad989a457ee498f26ad0f6e004f786
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264939"
---
# <a name="get-an-event-hubs-connection-string"></a>Hämta en anslutningssträng för händelsehubbar

Om du vill använda händelsehubbar måste du skapa ett namnområde för eventhubbar. Ett namnområde är en omfångsbehållare för flera händelsehubbar eller Kafka-ämnen. Det här namnområdet ger dig en unik [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). När ett namnområde har skapats kan du hämta den anslutningssträng som krävs för att kommunicera med eventhubbar.

Anslutningssträngen för Azure Event Hubs har följande komponenter inbäddade i den,

* FQDN = FQDN för namnområdet EventHubs som du skapade (det innehåller namnet EventHubs-namnområde följt av servicebus.windows.net)
* SharedAccessKeyName = namnet du valde för programmets SAS-nycklar
* SharedAccessKey = det genererade värdet för nyckeln.

Anslutningssträngmallen ser ut som
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

En exempelanslutningssträng kan se ut så här`Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Den här artikeln går igenom olika sätt att få anslutningssträngen.

## <a name="get-connection-string-from-the-portal"></a>Hämta anslutningssträng från portalen
1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Välj **Alla tjänster** på menyn till vänster. 
3. Välj **Händelsehubbar** i avsnittet **Analytics.** 
4. Välj händelsehubben i listan över händelsehubbar.
6. På sidan **Namnområde för händelsehubbar** väljer du **Principer för delad åtkomst** på den vänstra menyn.

    ![Menyalternativ för delade åtkomstprinciper](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Välj en **princip för delad åtkomst** i listan över principer. Standardnamnet: **RootManageSharedAccessPolicy**. Du kan lägga till en princip med rätt behörighet (läsa, skriva) och använda den principen. 

    ![Principer för delad åtkomst till eventhubbar](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Markera **kopieringsknappen** bredvid fältet **Anslutningssträngprimärnyckel.** 

    ![Event Hubs - hämta anslutningssträng](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Hämta anslutningssträngen med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan använda [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) för att hämta anslutningssträngen för det specifika princip-/regelnamnet som visas nedan:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Hämta anslutningssträngen med Azure CLI
Du kan använda följande för att hämta anslutningssträngen för namnområdet:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Du kan också använda följande för att hämta anslutningssträngen för en EventHub-entitet:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Mer information om Azure CLI-kommandon för eventhubbar finns i [Azure CLI for Event Hubs](/cli/azure/eventhubs).

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs](event-hubs-what-is-event-hubs.md)
* [Skapa en händelsehubb](event-hubs-create.md)
