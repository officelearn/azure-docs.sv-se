---
title: Hämta anslutningssträngen – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller anvisningar för att få en anslutningssträng som klienter kan använda för att ansluta till Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: edd197fb6d578df064c67a422767e3e70a0c8142
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445108"
---
# <a name="get-an-event-hubs-connection-string"></a>Hämta en anslutningssträng för Event Hubs

Om du vill använda Event Hubs, måste du skapa ett namnområde för Event Hubs. Ett namnområde är en gemensam behållare för flera händelsehubbar eller Kafka-avsnitt. Det här namnområdet får du ett unikt [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). När ett namnområde har skapats kan hämta du anslutningssträngen som krävs för att kommunicera med Event Hubs.

Anslutningssträngen för Azure Event Hubs har följande komponenter som är inbäddad i den,

* FQDN = det fullständiga Domännamnet för EventHubs-namnområdet som du skapade (som innehåller namnet för namnområdet EventHubs, följt av servicebus.windows.net)
* SharedAccessKeyName = namnet som du har valt för ditt programs SAS-nycklar
* SharedAccessKey = det genererade värdet för nyckeln.

Mallen anslutningssträngen ut
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

En exempel-anslutningssträng kan se ut `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Den här artikeln vägleder dig genom olika sätt för att hämta anslutningssträngen.

## <a name="get-connection-string-from-the-portal"></a>Hämta anslutningssträng från portalen
1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Välj **alla tjänster** på menyn till vänster navigering. 
3. Välj **Händelsehubbar** i den **Analytics** avsnittet. 
4. I listan över händelsehubbar, väljer du din event hub.
6. På den **Event Hubs Namespace** väljer **delade åtkomstprinciper** på den vänstra menyn.

    ![Delade åtkomstprinciper menyalternativ](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Välj en **delad åtkomstprincip** i listan över principer. Standard som en har namnet: **RootManageSharedAccessPolicy**. Du kan lägga till en princip med rätt behörighet (läsa, skriva) och använder principen. 

    ![Händelsehubbar delade åtkomstprinciper](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Välj den **kopia** knappen bredvid den **anslutningssträng – primär nyckel** fält. 

    ![Event Hubs – hämta anslutningssträngen](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Hämta anslutningssträngen med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan använda den [Get-AzEventHubNamespaceKey](/powershell/module/az.eventhub/get-azeventhubkey) att hämta anslutningssträngen för specifika principregeln namnet som visas nedan:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Hämta anslutningssträngen med Azure CLI
Du kan använda följande för att hämta anslutningssträngen för namnområdet:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Läs mer om Azure CLI-kommandon för Event Hubs, [Azure CLI för Event Hubs](/cli/azure/eventhubs).

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Skapa en händelsehubb](event-hubs-create.md)
