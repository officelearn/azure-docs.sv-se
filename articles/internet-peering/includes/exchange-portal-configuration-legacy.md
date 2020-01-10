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
ms.openlocfilehash: 7d7b9f847cdcc4ab4b1ff065425eebe07fb4d888
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775217"
---
1. På sidan **skapa en peering** , under fliken **konfiguration** , fyller du i fälten som visas nedan.

    > [!div class="mx-imgBorder"]
    > ![peering-konfiguration – Exchange](../media/setup-exchange-conf-tab.png)

    * För **peering-typ**väljer du *Exchange*.
    * Välj **SKU** som *standard kostnads fritt*.
    * Välj den **tunnelbane** plats där du vill konvertera peering till Azure-resursen. Om du har peering-anslutningar med Microsoft på den valda **Metro** -platsen som inte konverteras till Azure-resursen visas sådana anslutningar i avsnittet **peering Connections** (se nedan). Nu kan du konvertera peering-anslutningarna till Azure-resursen.

        > [!div class="mx-imgBorder"]
        > ![peering-konfiguration-Exchange-bakåtkompatibelt anslutningar](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Det går inte att ändra inställningar för äldre peering-anslutningar. Om du vill lägga till ytterligare peering-anslutningar med Microsoft på den valda **Metro** -platsen kan du göra det genom att klicka på knappen **Skapa ny** . Mer information finns i [skapa eller ändra en Exchange-peering med hjälp av portalen](../howto-exchange-portal.md) .
        >

1. Klicka på **Granska + skapa**. Observera att portalen kör grundläggande verifiering av den information som du har angett. Detta visas i ett menyfliksområde överst, som *körning av slutgiltig verifiering...* .

    > [!div class="mx-imgBorder"]
    > Fliken ![-peering-verifiering](../media/setup-direct-review-tab-validation.png)

1. När den har *blivit klar med verifieringen*verifierar du din information och skickar in begäran genom att klicka på **skapa**. Om du behöver ändra din begäran klickar du på **föregående** och upprepar stegen ovan.

    > [!div class="mx-imgBorder"]
    > ![peering-sändning](../media/setup-exchange-review-tab-submit.png)

1. När du har skickat begäran väntar du tills den har slutfört distributionen. Om distributionen Miss lyckas, kontakta [Microsoft-peering](mailto:peering@microsoft.com). En lyckad distribution visas som nedan.

    > [!div class="mx-imgBorder"]
    > ![-peering lyckades](../media/setup-direct-success.png)
