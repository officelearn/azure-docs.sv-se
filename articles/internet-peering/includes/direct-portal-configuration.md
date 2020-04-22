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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681032"
---
1. På sidan **Skapa en peering** på fliken **Konfiguration** fyller du i rutorna som visas här.

    * För **peering-typ**väljer du **Direkt**.
    * För **Microsoft-nätverk**väljer du **AS8075**. Skapa inte peering med ASN 8069. Den är reserverad för speciella program och används endast av [Microsoft peering](mailto:peering@microsoft.com).
    * Välj **SKU** som **Basic Free**. Välj inte Premium Free eftersom det är reserverat för speciella program.
    * Välj den **Metro-plats** där du vill ställa in peering.

        > [!NOTE]
        > Om du redan har peering-anslutningar med Microsoft på den valda **Metro-platsen** och du använder Azure-portalen för första gången för att konfigurera peering på den platsen, visas dina befintliga peering-anslutningar i avsnittet **Peering-anslutningar** som visas. Microsoft konverterar automatiskt dessa peering-anslutningar till en Azure-resurs så att du kan hantera dem alla, tillsammans med de nya anslutningarna, på ett ställe. Mer information finns i [Konvertera en äldre Direct-peering till en Azure-resurs med hjälp av portalen](../howto-legacy-direct-portal.md).
        >

1. Under **Peering-anslutningar**väljer du **Skapa ny** om du vill lägga till en rad för varje ny anslutning som du vill konfigurera.

    * Om du vill konfigurera eller ändra anslutningsinställningar markerar du redigeringsknappen för en rad.

        > [!div class="mx-imgBorder"]
        > ![Knappen Redigera](../media/setup-direct-conf-tab-edit.png)
    
    * Om du vill ta bort en rad markerar du **...**  >  **Ta bort**.

        > [!div class="mx-imgBorder"]
        > ![Knappen Ta bort](../media/setup-direct-conf-tab-delete.png)

    * Du måste ange alla inställningar för en anslutning, som visas här.

         > [!div class="mx-imgBorder"]
         > ![Sidan Direkt peering-anslutning](../media/setup-direct-conf-tab-connection.png)

        1. Välj den **peering-anläggning** där anslutningen måste ställas in.
        1. **Sessionsadressprovider** används för att avgöra vem som tillhandahåller det undernät som behövs för att konfigurera BGP-sessionen mellan nätverket och Microsoft. Om du kan tillhandahålla undernätet väljer du **Peer**. Annars väljer du **Microsoft** och [Microsoft-peering](mailto:peering@microsoft.com) kontaktar dig. Det tar längre tid för Microsoft att bearbeta peering-begäran om att välja det här alternativet. I vissa fall kanske Microsoft inte kan tillhandahålla undernät, vilket resulterar i begäran avslag.
        1. Om du har valt alternativet **Sessionsadressprovider** som **Peer**anger du IPv4- och IPv6-adresserna tillsammans med prefixmaskerna i **prefixrutorna Session IPv4** och **Prefixet Session IPv6.**
        1. Ange antalet IPv4- och IPv6-prefix som du ska annonsera i **rutorna Maximalt annonserade IPv4-adresser** och **Maximalt annonserade IPv6-adresser.**
        1. Justera skjutreglaget **Total bandbredd** så att det återspeglar anslutningens bandbredd.
        1. Välj **Spara** om du vill spara anslutningsinställningarna.

1. Upprepa föregående steg för att lägga till fler anslutningar på alla anläggningar där Microsoft är samlokaliseras med ditt nätverk, inom **den Metro** som du valde tidigare.

1. När du har lagt till alla nödvändiga anslutningar väljer du **Granska + skapa**.

    > [!div class="mx-imgBorder"]
    > ![Fliken Peering-konfiguration slutlig](../media/setup-direct-conf-tab-final.png)

1. Observera att portalen kör grundläggande validering av den information du angav. Ett menyfliksområdet högst upp visar meddelandet *Kör slutlig validering...*.

    > [!div class="mx-imgBorder"]
    > ![Fliken Validering av peering](../media/setup-direct-review-tab-validation.png)

1. När meddelandet har ändrades till *Valideringen har du*verifierat din information. Skicka begäran genom att välja **Skapa**. Om du vill ändra din begäran väljer du **Föregående** och upprepar stegen.

    > [!div class="mx-imgBorder"]
    > ![Peering-inlämning](../media/setup-direct-review-tab-submit.png)

1. När du har skickat begäran väntar du tills distributionen är klar. Om distributionen misslyckas kontaktar du [Microsoft peering](mailto:peering@microsoft.com). En lyckad distribution visas som visas här.

    > [!div class="mx-imgBorder"]
    > ![Peering framgång](../media/setup-direct-success.png)
