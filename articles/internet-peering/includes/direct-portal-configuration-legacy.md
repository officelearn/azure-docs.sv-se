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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775074"
---
1. På sidan **Skapa en peering** under **fliken Konfiguration** fyller du i fälten som visas nedan.

    > [!div class="mx-imgBorder"]
    > ![Peering-konfiguration - Direkt](../media/setup-direct-conf-tab.png)

    * För **peering-typ**väljer du *Direkt*.
    * För **Microsoft-nätverk**väljer du *AS8075*. Välj inte ASN 8069. Den är reserverad för speciella program och används endast av [Microsoft peering](mailto:peering@microsoft.com).
    * Välj **SKU** som *Basic Free*. Välj inte *Premium Free* eftersom det är reserverat för speciella applikationer.
    * Välj **Metro-platsen** för den plats där du vill konvertera peering till Azure-resurs. Om du har peering-anslutningar med Microsoft på den valda **Metro-platsen** som inte konverteras till Azure-resurs, visas sådana anslutningar i avsnittet **Peering-anslutningar** enligt nedan. Du kan nu konvertera dessa peering-anslutningar till Azure-resurs.

        > [!div class="mx-imgBorder"]
        > ![Peering-konfiguration - Direkt - äldre anslutningar](../media/setup-directlegacy-conf-tab.png)

1. Om du behöver uppdatera bandbredden klickar du på redigeringsknappen för en rad, som markerats nedan, för att ändra anslutningsinställningarna.

    > [!div class="mx-imgBorder"]
    > ![Peering-konfiguration – direktredigering](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Om du vill lägga till ytterligare peering-anslutningar med Microsoft på den valda **Metro-platsen** kan du göra det genom att klicka på **Skapa ny** knapp. Mer information [finns i Skapa eller ändra en direkt peering med hjälp av portalen.](../howto-direct-portal.md)
    >

1. Klicka på **Granska + skapa**. Observera att portalen kör grundläggande validering av den information du angav. Detta visas i ett band på toppen, som *Kör slutlig validering...*.

    > [!div class="mx-imgBorder"]
    > ![Fliken Validering av peering](../media/setup-direct-review-tab-validation.png)

1. När den har överförts till *Godkänd validering*verifierar du din information och skickar begäran genom att klicka på **Skapa**. Om du behöver ändra din begäran klickar du på **Föregående** och upprepar stegen ovan.

    > [!div class="mx-imgBorder"]
    > ![Skicka in peering](../media/setup-direct-review-tab-submit.png)

1. När du har skickat begäran väntar du tills den har slutfört distributionen. Om distributionen misslyckas kontaktar du [Microsoft peering](mailto:peering@microsoft.com). En lyckad distribution visas enligt nedan.

    > [!div class="mx-imgBorder"]
    > ![Peering framgång](../media/setup-direct-success.png)
