---
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: include
ms.date: 12/03/2019
ms.openlocfilehash: 21811041a25c63bb7542b101812222a9430c20fe
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76887631"
---
1. Om du vill skapa ett nytt konfigurations Arkiv för appen loggar du in på [Azure Portal](https://portal.azure.com). I det övre vänstra hörnet på Start sidan väljer du **skapa en resurs**. I rutan **Sök på Marketplace anger du** **app-konfiguration** och väljer RETUR.

    ![Sök efter App Configuration](media/azure-app-configuration-create/azure-portal-search.png)

1. Välj **app-konfiguration** från Sök resultaten och välj sedan **skapa**.

    ![Välj Skapa](media/azure-app-configuration-create/azure-portal-app-configuration-create.png)

1. I fönstret **app Configuration** > **skapa** anger du följande inställningar:

    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Resursnamn** | Globalt unikt namn | Ange ett unikt resurs namn som ska användas för appens konfigurations Arkiv resurs. Namnet måste vara en sträng mellan 5 och 50 tecken och får bara innehålla siffror, bokstäver och `-` tecken. Namnet får inte börja eller sluta med `-` Character.  |
    | **Prenumeration** | Din prenumeration | Välj den Azure-prenumeration du vill använda för att testa App Configuration. Om ditt konto bara har en prenumeration väljs den automatiskt och ingen **prenumerations** lista visas. |
    | **Resursgrupp** | *AppConfigTestResources* | Välj eller skapa en resurs grupp för din app Configuration Store-resurs. Den här gruppen är användbar för att organisera flera resurser som du kanske vill ta bort samtidigt genom att ta bort resurs gruppen. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser](/azure/azure-resource-manager/resource-group-overview). |
    | **Plats** | *USA, centrala* | Använd **Plats** till att ange den geografiska plats där appkonfigurationsarkivets resurs finns. För bästa prestanda skapar du resursen i samma region som andra komponenter i ditt program. |

    ![Skapa en resurs för konfigurations Arkiv för app](media/azure-app-configuration-create/azure-portal-app-configuration-create-settings.png)

1. Välj **Skapa**. Distributionen kan ta några minuter.

1. När distributionen är klar väljer du **inställningar** > **åtkomst nycklar**. Anteckna den primära skrivskyddade nyckel anslutnings strängen. Du kommer att använda den här anslutnings strängen senare för att konfigurera programmet för att kommunicera med det app-konfigurations lager som du har skapat.
