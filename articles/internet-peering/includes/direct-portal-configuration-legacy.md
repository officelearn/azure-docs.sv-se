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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678837"
---
1. På sidan **Skapa en peering** på fliken **Konfiguration** fyller du i rutorna som visas här.

    > [!div class="mx-imgBorder"]
    > ![Skapa fliken Konfiguration av peering-sidor](../media/setup-direct-conf-tab.png)

    * För **peering-typ**väljer du **Direkt**.
    * För **Microsoft-nätverk**väljer du **AS8075**. Välj inte ASN 8069. Den är reserverad för speciella program och används endast av [Microsoft peering](mailto:peering@microsoft.com).
    * Välj **SKU** som **Basic Free**. Välj inte Premium Free eftersom det är reserverat för speciella program.
    * Välj den **Metro-plats** där du vill konvertera peering till en Azure-resurs. Om du har peering-anslutningar med Microsoft på den valda **Metro-platsen** som inte konverteras till en Azure-resurs visas dessa anslutningar i avsnittet **Peering-anslutningar** som visas. Du kan nu konvertera dessa peering-anslutningar till en Azure-resurs.

        > [!div class="mx-imgBorder"]
        > ![Lista över peering-anslutningar](../media/setup-directlegacy-conf-tab.png)

1. Om du behöver uppdatera bandbredden väljer du redigeringsknappen för en linje för att ändra anslutningsinställningarna.

    > [!div class="mx-imgBorder"]
    > ![Knappen Redigera](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Om du vill lägga till ytterligare peering-anslutningar med Microsoft på den valda **Metro-platsen** väljer du **Skapa nya**. Mer information finns i [Skapa eller ändra en direkt peering med hjälp av portalen](../howto-direct-portal.md).
    >

1. Välj **Granska + skapa**. Observera att portalen kör grundläggande validering av den information du angav. Ett menyfliksområdet högst upp visar meddelandet *Kör slutlig validering...*.

    > [!div class="mx-imgBorder"]
    > ![Fliken Validering av peering](../media/setup-direct-review-tab-validation.png)

1. När meddelandet har ändrades till *Valideringen har du*verifierat din information. Skicka begäran genom att välja **Skapa**. Om du vill ändra din begäran väljer du **Föregående** och upprepar stegen.

    > [!div class="mx-imgBorder"]
    > ![Peering-inlämning](../media/setup-direct-review-tab-submit.png)

1. När du har skickat begäran väntar du tills distributionen är klar. Om distributionen misslyckas kontaktar du [Microsoft peering](mailto:peering@microsoft.com). En lyckad distribution visas som visas här.

    > [!div class="mx-imgBorder"]
    > ![Peering framgång](../media/setup-direct-success.png)
