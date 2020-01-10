---
title: ta med fil
titleSuffix: Azure
description: ta med fil
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 16909e0a5d59b0ae019d91aad00e0168b0c5f433
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775074"
---
1. På sidan **skapa en peering** , under fliken **konfiguration** , fyller du i fälten som visas nedan.

    > [!div class="mx-imgBorder"]
    > ![peering-konfiguration – direkt](../media/setup-direct-conf-tab.png)

    * För **peering-typ**väljer du *Direct*.
    * För **Microsoft Network**väljer du *AS8075*. Välj inte ASN 8069. Den är reserverad för särskilda program och används endast av [Microsoft-peering](mailto:peering@microsoft.com).
    * Välj **SKU** som *standard kostnads fritt*. Välj inte *Premium kostnads fritt* eftersom det är reserverat för särskilda program.
    * Välj den **tunnelbane** plats där du vill konvertera peering till Azure-resursen. Om du har peering-anslutningar med Microsoft på den valda **Metro** -platsen som inte konverteras till Azure-resursen visas sådana anslutningar i avsnittet **peering Connections** (se nedan). Nu kan du konvertera peering-anslutningarna till Azure-resursen.

        > [!div class="mx-imgBorder"]
        > ![peering-konfiguration – direkta äldre anslutningar](../media/setup-directlegacy-conf-tab.png)

1. Om du behöver uppdatera bandbredd klickar du på knappen Redigera för en rad, enligt beskrivningen nedan, för att ändra anslutnings inställningarna.

    > [!div class="mx-imgBorder"]
    > ![peering-konfiguration – direkt redigering](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Om du vill lägga till ytterligare peering-anslutningar med Microsoft på den valda **Metro** -platsen kan du göra det genom att klicka på knappen **Skapa ny** . Mer information finns i [skapa eller ändra en direkt peering med hjälp av portalen](../howto-direct-portal.md) .
    >

1. Klicka på **Granska + skapa**. Observera att portalen kör grundläggande verifiering av den information som du har angett. Detta visas i ett menyfliksområde överst, som *körning av slutgiltig verifiering...* .

    > [!div class="mx-imgBorder"]
    > Fliken ![-peering-verifiering](../media/setup-direct-review-tab-validation.png)

1. När den har *blivit klar med verifieringen*verifierar du din information och skickar in begäran genom att klicka på **skapa**. Om du behöver ändra din begäran klickar du på **föregående** och upprepar stegen ovan.

    > [!div class="mx-imgBorder"]
    > ![peering-sändning](../media/setup-direct-review-tab-submit.png)

1. När du har skickat begäran väntar du tills den har slutfört distributionen. Om distributionen Miss lyckas, kontakta [Microsoft-peering](mailto:peering@microsoft.com). En lyckad distribution visas som nedan.

    > [!div class="mx-imgBorder"]
    > ![-peering lyckades](../media/setup-direct-success.png)
