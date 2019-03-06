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
ms.openlocfilehash: 71e63de247e05a4712687354ed548219b36e8f2f
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885130"
---
1. För att skapa ett nytt appkonfigurationsarkiv loggar du först in på [Azure-portalen](https://aka.ms/azconfig/portal). Klicka på **+ Skapa en resurs** uppe till vänster på sidan. I textrutan **Sök på Marketplace** skriver du **App Configuration** och trycker på **Enter**.

    ![Sök efter App Configuration](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-new.png)

2. Klicka på **App Configuration** från sökresultatet och sedan på **Skapa**.

3. På sidan **App Configuration** > **Skapa** anger du följande inställningar:

    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Resursnamn** | Globalt unikt namn | Ange ett unikt resursnamn som ska användas för appkonfigurationsarkivets resurs. Namnet måste vara en sträng mellan 1 och 63 tecken och får endast innehålla siffror, bokstäver och `-`-tecknet. Namnet får inte inledas eller avslutas med `-`-tecknet eller ha flera `-`-tecken i följd.  |
    | **Prenumeration** | Din prenumeration | Välj den Azure-prenumeration du vill använda för att testa App Configuration. Om ditt konto bara har en prenumeration väljs den automatiskt och listrutan **Prenumeration** visas inte. |
    | **Resursgrupp** | *AppConfigTestResources* | Välj eller skapa en resursgrupp för appkonfigurationsarkivets resurs. Den här gruppen är användbar när du vill ta bort flera resurser samtidigt genom att ta bort resursgruppen. Mer information finns i [Använda resursgrupper för att hantera Azure-resurser](/azure/azure-resource-manager/resource-group-overview). |
    | **Plats** | *USA, centrala* | Använd **Plats** till att ange den geografiska plats där appkonfigurationsarkivets resurs finns. Du får bästa prestanda om du skapar resursen i samma region som de andra komponenterna i appen. |

    ![Skapar en resurs för ett appkonfigurationsarkiv](../articles/azure-app-configuration/media/quickstarts/azure-app-configuration-create.png)

4. Klicka på **Skapa**. Det kan ta några minuter att slutföra distributionen.

5. När distributionen är klar klickar du på **Inställningar** > **Åtkomstnycklar**. Anteckna anslutningssträngen för antingen den skrivskyddade primärnyckeln eller primärnyckeln för läsning/skrivning. Du använder det här senare för att konfigurera programmet till att kommunicera med det appkonfigurationsarkiv som du just har skapat.

6. Klicka på **Key/Value Explorer** (Utforskare för nyckel/värde) och **+ Skapa** för att lägga till följande nyckel/värde-par:

    | Nyckel | Värde |
    |---|---|
    | TestApp:Settings:BackgroundColor | white |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | svart |
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Låta **Label** (Etikett) och **Content Type** (Innehållstyp) vara tomma för tillfället.
