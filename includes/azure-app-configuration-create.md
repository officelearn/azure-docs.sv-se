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
ms.openlocfilehash: 9b86f2e05e2cb42470061bd6398b4200607f2418
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60202253"
---
1. Om du vill skapa en ny konfiguration appbutik, logga in på den [Azure-portalen](https://aka.ms/azconfig/portal). I det övre vänstra hörnet på sidan Välj **+ skapa en resurs**. I den **Sök på Marketplace** anger **Appkonfiguration** och tryck på RETUR.

    ![Sök efter App Configuration](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Välj **Appkonfiguration** sökresultat och välj sedan **skapa**.

3. På den **Appkonfiguration** > **skapa** anger du följande inställningar.

    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Resursnamn** | Globalt unikt namn | Ange ett unikt resursnamn som ska användas för appkonfigurationsarkivets resurs. Namnet måste vara en sträng mellan 1 och 63 tecken och får endast innehålla siffror, bokstäver och `-`-tecknet. Namnet får inte börja eller sluta med den `-` tecknet eller efterföljande `-` tecken är ogiltiga.  |
    | **Prenumeration** | Din prenumeration | Välj den Azure-prenumeration du vill använda för att testa App Configuration. Om ditt konto bara har en prenumeration, det väljs automatiskt och **prenumeration** inte visas i listrutan. |
    | **Resursgrupp** | *AppConfigTestResources* | Välj eller skapa en resursgrupp för appkonfigurationsarkivets resurs. Den här gruppen är användbart för att ordna flera resurser som du kanske vill ta bort samtidigt genom att ta bort resursgruppen. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser](/azure/azure-resource-manager/resource-group-overview). |
    | **Plats** | *USA, centrala* | Använd **Plats** till att ange den geografiska plats där appkonfigurationsarkivets resurs finns. Skapa resursen i samma region som andra komponenter i ditt program för bästa prestanda. |

    ![Skapar en resurs för ett appkonfigurationsarkiv](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Välj **Skapa**. Distributionen kan ta några minuter att slutföra.

5. När distributionen är klar, Välj **inställningar** > **åtkomstnycklar**. Notera antingen eller primära skrivskyddade primärnyckelns anslutningssträng. Du kan använda den här anslutningssträngen senare för att konfigurera ditt program kan kommunicera med appbutiken för konfigurationen som du skapade.
