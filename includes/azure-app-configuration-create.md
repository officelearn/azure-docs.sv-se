---
title: ta med fil
description: ta med fil
services: azure-app-configuration
author: yegu
ms.service: azure-app-configuration
ms.topic: include
ms.date: 01/22/2019
ms.author: yegu
ms.custom: include file
ms.openlocfilehash: c98a17be394887ef4e008b079467c85d4ded7e09
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393314"
---
1. Om du vill skapa en ny konfiguration av butik, logga in på den [Azure-portalen](https://portal.azure.com). I det övre vänstra hörnet i fönstret Välj **+ skapa en resurs**. I den **Sök på Marketplace** anger **Appkonfiguration** och välj RETUR.

    ![Sök efter App Configuration](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

1. Välj **Appkonfiguration** sökresultat och välj sedan **skapa**.

1. På den **Appkonfiguration** > **skapa** fönstret anger du följande inställningar:

    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Resursnamn** | Globalt unikt namn | Ange ett unikt resursnamn ska användas för konfiguration av store-resursen. Namnet måste vara en sträng mellan 1 och 63 tecken och får endast innehålla siffror, bokstäver och `-`-tecknet. Namnet får inte börja eller sluta med den `-` tecknet eller efterföljande `-` tecken är ogiltiga.  |
    | **Prenumeration** | Din prenumeration | Välj den Azure-prenumeration du vill använda för att testa App Configuration. Om ditt konto bara har en prenumeration, det väljs automatiskt och **prenumeration** listan inte visas. |
    | **Resursgrupp** | *AppConfigTestResources* | Välj eller skapa en resursgrupp för din konfiguration av store-resurs. Den här gruppen är användbart för att ordna flera resurser som du kanske vill ta bort samtidigt genom att ta bort resursgruppen. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser](/azure/azure-resource-manager/resource-group-overview). |
    | **Plats** | *USA, centrala* | Använd **Plats** till att ange den geografiska plats där appkonfigurationsarkivets resurs finns. Skapa resursen i samma region som andra komponenter i ditt program för bästa prestanda. |

    ![Skapa en konfiguration av store-resurs](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

1. Välj **Skapa**. Distributionen kan ta några minuter att slutföra.

1. När distributionen är klar, Välj **inställningar** > **åtkomstnycklar**. Notera antingen eller primära skrivskyddade primärnyckelns anslutningssträng. Du kommer att använda den här anslutningssträngen senare för att konfigurera ditt program kan kommunicera med arkivet konfiguration som du skapade.
