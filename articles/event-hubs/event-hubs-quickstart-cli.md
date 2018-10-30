---
title: Snabbstart om Azure – Skapa en händelsehubb med Azure CLI | Microsoft Docs
description: Den här snabbstarten beskriver hur du skapar en händelsehubb med Azure CLI och sedan skickar och tar emot händelser med Java.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 6861452f176e530dcdcba8ebddf4d2edadb3a6a2
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456345"
---
# <a name="quickstart-create-an-event-hub-using-azure-cli"></a>Snabbstart: Skapa en händelsehubb med Azure CLI

Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här snabbstarten har du skapat en händelsehubb med hjälp av Azure CLI.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du behöver en Azure-prenumeration för att kunna utföra den här snabbstarten. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto][] innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda Azure CLI lokalt kräver de här självstudierna att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att kontrollera vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Följande steg krävs inte om du kör kommandon i Cloud Shell. Om du kör CLI lokalt måste du utföra följande steg för att logga in i Azure och ange din aktuella prenumeration:

Kör följande kommandon för att logga in på Azure:

```azurecli-interactive
az login
```

Ange den aktuella prenumerationskontexten. Ersätt `MyAzureSub` med namnet på den Azure-prenumeration som du vill använda:

```azurecli-interactive
az account set --subscription MyAzureSub
``` 

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
En resursgrupp är en logisk samling Azure-resurser. Alla resurser distribueras och hanteras i en resursgrupp. Kör följande kommando för att skapa en resursgrupp:

```azurecli-interactive
# Create a resource group. Specify a name for the resource group.
az group create --name <resource group name> --location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde
Ett Event Hubs-namnområde innehåller en unik omfattningscontainer som refereras till av dess fullständigt kvalificerade domännamn, där du skapar en eller flera händelsehubbar. Skapa en namnrymd i resursgruppen genom att köra följande kommando:

```azurecli-interactive
# Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
```

## <a name="create-an-event-hub"></a>Skapa en händelsehubb
Kör följande kommando för att skapa en händelsehubb:

```azurecli-interactive
# Create an event hub. Specify a name for the event hub. 
az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
```

Grattis! Du har skapat ett Event Hubs-namnområde och en händelsehubb i namnområdet med Azure CLI. 

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en resursgrupp, en Event Hubs-namnrymd och en händelsehubb. Stegvisa instruktioner för att skicka händelser till eller ta emot händelser från en händelsehubb finns i följande självstudier:  

- **Skicka händelser till en händelsehubb**: [.NET Standard](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **Ta emot händelser från en händelsehubb**: [.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)

[Skapa ett kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
