---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 089a199da08503e13dbd42518970c3b7ed8a984d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77619305"
---
1. Om du vill skapa ett nytt App Configuration Store loggar du in på [Azure-portalen](https://portal.azure.com). I det övre vänstra hörnet på startsidan väljer du **Skapa en resurs**. I rutan **Sök på Marketplace** anger du **Appkonfiguration** och väljer Retur.

    ![Sök efter App Configuration](media/azure-app-configuration-create/azure-portal-search.png)

1. Välj **Appkonfiguration** i sökresultaten och välj sedan **Skapa**.

    ![Välj Skapa](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. Ange följande inställningar i fönstret**Skapa** **appkonfiguration:** > 

    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Resursnamn** | Globalt unikt namn | Ange ett unikt resursnamn som ska användas för appkonfigurationsarkivets resurs. Namnet måste vara en sträng mellan 5 och 50 tecken `-` och innehåller endast siffror, bokstäver och tecken. Namnet kan inte börja eller `-` sluta med tecknet.  |
    | **Prenumeration** | Din prenumeration | Välj den Azure-prenumeration du vill använda för att testa App Configuration. Om ditt konto bara har en prenumeration väljs det automatiskt och **prenumerationslistan** visas inte. |
    | **Resursgrupp** | *AppConfigTestResources* | Markera eller skapa en resursgrupp för appkonfigurationsbutikens resurs. Den här gruppen är användbar när du vill ordna flera resurser som du kanske vill ta bort samtidigt genom att ta bort resursgruppen. Mer information finns i [Använda resursgrupper för att hantera dina Azure-resurser](/azure/azure-resource-manager/resource-group-overview). |
    | **Location** | *USA, centrala* | Använd **Plats** till att ange den geografiska plats där appkonfigurationsarkivets resurs finns. För bästa prestanda kan du skapa resursen i samma region som andra komponenter i ditt program. |
    | **Prisnivå** | *Gratis* | Välj önskad prisnivå. Mer information finns på [prissidan för appkonfiguration](https://azure.microsoft.com/pricing/details/app-configuration/).

    ![Skapa en appkonfigurationsarkivresurs](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Välj **Skapa**. Distributionen kan ta några minuter.

1. När distributionen är klar väljer du **Inställningar** > **Access Nycklar**. Anteckna den primära skrivskyddade nyckelanslutningssträngen. Du ska använda den här anslutningssträngen senare för att konfigurera programmet så att det kommunicerar med appkonfigurationsarkivet som du skapade.
