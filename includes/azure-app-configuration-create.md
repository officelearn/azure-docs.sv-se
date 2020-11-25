---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 1/31/2020
ms.openlocfilehash: 48bf5d5df3c54e8b2894ad28fa08553d5add0437
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001495"
---
1. Om du vill skapa ett nytt konfigurations Arkiv för appen loggar du in på [Azure Portal](https://portal.azure.com). I det övre vänstra hörnet på Start sidan väljer du **skapa en resurs**. I rutan **Sök på Marketplace anger du** *app-konfiguration* och väljer <kbd>RETUR</kbd>.

    ![Sök efter App Configuration](media/azure-app-configuration-create/azure-portal-search.png)

1. Välj **app-konfiguration** från Sök resultaten och välj sedan **skapa**.

    ![Välj Skapa](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. I fönstret **skapa app-konfiguration** anger du följande inställningar:

    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Prenumeration** | Din prenumeration | Välj den Azure-prenumeration du vill använda för att testa App Configuration. Om ditt konto bara har en prenumeration väljs den automatiskt och ingen **prenumerations** lista visas. |
    | **Resursgrupp** | *AppConfigTestResources* | Välj eller skapa en resurs grupp för din app Configuration Store-resurs. Den här gruppen är användbar för att organisera flera resurser som du kanske vill ta bort samtidigt genom att ta bort resurs gruppen. Mer information finns i [använda resurs grupper för att hantera dina Azure-resurser](../articles/azure-resource-manager/management/overview.md). |
    | **Resurs namn** | Globalt unikt namn | Ange ett unikt resurs namn som ska användas för appens konfigurations Arkiv resurs. Namnet måste vara en sträng mellan 5 och 50 tecken och får bara innehålla siffror, bokstäver och `-` tecknet. Namnet får inte börja eller sluta med `-` specialtecknet. |
    | **Plats** | *USA, centrala* | Använd **Plats** till att ange den geografiska plats där appkonfigurationsarkivets resurs finns. För bästa prestanda skapar du resursen i samma region som andra komponenter i ditt program. |
    | **Prisnivå** | *Kostnadsfri* | Välj önskad pris nivå. Mer information finns på sidan med [prissättning för app-konfiguration](https://azure.microsoft.com/pricing/details/app-configuration). |

1. Välj **Granska + skapa** för att validera inställningarna.

1. Välj **Skapa**. Distributionen kan ta några minuter.

1. När distributionen är klar går du till appen konfigurations resurs. Välj **Inställningar** > **Åtkomstnycklar**. Anteckna den primära skrivskyddade nyckel anslutnings strängen. Du kommer att använda den här anslutnings strängen senare för att konfigurera programmet för att kommunicera med det app-konfigurations lager som du har skapat.