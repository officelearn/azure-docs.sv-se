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
ms.openlocfilehash: 77a768f907ad989a457ee498f26ad0f6e004f786
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79264939"
---
# <a name="get-an-event-hubs-connection-string"></a>Hämta en anslutningssträng för Event Hubs

Om du vill använda Event Hubs, måste du skapa ett namnområde för Event Hubs. Ett namn område är en omfattnings behållare för flera Event Hub-eller Kafka-ämnen. Det här namn området ger dig ett unikt [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). När ett namnområde har skapats kan hämta du anslutningssträngen som krävs för att kommunicera med Event Hubs.

Anslutningssträngen för Azure Event Hubs har följande komponenter som är inbäddad i den,

* FQDN = FQDN för namn området EventHubs som du skapade (det innehåller namn områdes namnet EventHubs följt av servicebus.windows.net)
* SharedAccessKeyName = namnet som du har valt för ditt programs SAS-nycklar
* SharedAccessKey = det genererade värdet för nyckeln.

Mallen anslutningssträngen ut
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Ett exempel på en anslutnings sträng kan se ut som `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Den här artikeln vägleder dig genom olika sätt för att hämta anslutningssträngen.

## <a name="get-connection-string-from-the-portal"></a>Hämta anslutningssträng från portalen
1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Välj **alla tjänster** i den vänstra navigerings menyn. 
3. Välj **Event Hubs** i avsnittet **analys** . 
4. I listan över händelse hubbar väljer du händelsehubben.
6. På sidan **Event Hubs namn område** väljer du **principer för delad åtkomst** på den vänstra menyn.

    ![Meny alternativet principer för delad åtkomst](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Välj en **princip för delad åtkomst** i listan över principer. Standardvärdet heter: **RootManageSharedAccessPolicy**. Du kan lägga till en princip med rätt behörigheter (läsa, skriva) och använda den principen. 

    ![Event Hubs principer för delad åtkomst](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Välj **kopierings** knappen bredvid fältet **anslutnings sträng – primär nyckel** . 

    ![Event Hubs – hämta anslutnings sträng](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Hämta anslutningssträngen med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du kan använda [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) för att hämta anslutnings strängen för den aktuella principen/regel namnet enligt nedan:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Hämta anslutningssträngen med Azure CLI
Du kan använda följande för att hämta anslutningssträngen för namnområdet:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Du kan också använda följande för att hämta anslutnings strängen för en EventHub-entitet:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Mer information om Azure CLI-kommandon för Event Hubs finns i [Azure CLI för Event Hubs](/cli/azure/eventhubs).

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Event Hubs-översikt](event-hubs-what-is-event-hubs.md)
* [Skapa en händelsehubb](event-hubs-create.md)
