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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680960"
---
1. På sidan **Skapa en peering** på fliken **Konfiguration** fyller du i rutorna som visas.

    > [!div class="mx-imgBorder"]
    > ![Skapa en peering-sida Exchange-peering-typ](../media/setup-exchange-conf-tab.png)

    * För **peering-typ**väljer du **Exchange**.
    * Välj **SKU** som **Basic Free**.
    * Välj den **Metro-plats** där du vill ställa in peering.

        > [!NOTE]
        > Om du redan har peering-anslutningar med Microsoft på den valda **Metro-platsen** och du använder portalen för första gången för att konfigurera peering på den platsen visas dina befintliga peering-anslutningar i avsnittet **Peering-anslutningar** enligt bilden. Microsoft konverterar automatiskt dessa peering-anslutningar till en Azure-resurs så att du kan hantera dem alla tillsammans med de nya anslutningarna på ett ställe. Mer information finns i [Konvertera en äldre Exchange-peering till en Azure-resurs med hjälp av portalen](../howto-legacy-exchange-portal.md).
        >

1. Under **Peering-anslutningar**väljer du **Skapa ny** om du vill lägga till en rad för varje ny anslutning som du vill konfigurera.

    * Om du vill konfigurera eller ändra anslutningsinställningar markerar du redigeringsknappen för en rad.

        > [!div class="mx-imgBorder"]
        > ![Knappen Redigera](../media/setup-exchange-conf-tab-edit.png)

    * Om du vill ta bort en rad markerar du **...**  >  **Ta bort**.

        > [!div class="mx-imgBorder"]
        > ![Knappen Ta bort](../media/setup-exchange-conf-tab-delete.png)

    * Du måste ange alla inställningar för en anslutning, som visas här.

         > [!div class="mx-imgBorder"]
         > ![Sidan Anslutningssida för Exchange Peering](../media/setup-exchange-conf-tab-connection.png)

        1. Välj den **peering-anläggning** där anslutningen måste ställas in.
        1. I **adressrutorna IPv4** och **IPv6** anger du de IPv4- respektive IPv6-adresser som skulle konfigureras i Microsoft-routrar med hjälp av grannkommandot.
        1. Ange antalet IPv4- och IPv6-prefix som du ska annonsera i **rutorna Maximalt annonserade IPv4-adresser** och **Maximalt annonserade IPv6-adresser.**
        1. Välj **OK** för att spara anslutningsinställningarna.

1. Upprepa steget för att lägga till fler anslutningar på alla anläggningar där Microsoft är samlokaliseras med ditt nätverk, inom den **Metro** som valts tidigare.

1. När du har lagt till alla nödvändiga anslutningar väljer du **Granska + skapa**.

    > [!div class="mx-imgBorder"]
    > ![Fliken Konfiguration för peering](../media/setup-exchange-conf-tab-final.png)

1. Observera att portalen kör grundläggande validering av den information du angav. Ett menyfliksområdet högst upp visar meddelandet *Kör slutlig validering...*.

    > [!div class="mx-imgBorder"]
    > ![Fliken Validering av peering](../media/setup-direct-review-tab-validation.png)

1. När meddelandet har ändrades till *Valideringen har du*verifierat din information. Skicka begäran genom att välja **Skapa**. Om du vill ändra din begäran väljer du **Föregående** och upprepar stegen.

    > [!div class="mx-imgBorder"]
    > ![Peering-inlämning](../media/setup-exchange-review-tab-submit.png)

1. När du har skickat begäran väntar du tills distributionen är klar. Om distributionen misslyckas kontaktar du [Microsoft peering](mailto:peering@microsoft.com). En lyckad distribution visas som visas här.

    > [!div class="mx-imgBorder"]
    > ![Peering framgång](../media/setup-direct-success.png)
