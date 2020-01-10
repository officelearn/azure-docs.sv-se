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
ms.openlocfilehash: cd51eca0ea4563e1b56f74677df0829669d9e177
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774502"
---
1. På sidan **skapa en peering** , under fliken **konfiguration** , fyller du i fälten som visas nedan.

    > [!div class="mx-imgBorder"]
    > ![peering-konfiguration – Exchange](../media/setup-exchange-conf-tab.png)

    * För **peering-typ**väljer du *Exchange*.
    * Välj **SKU** som *standard kostnads fritt*.
    * Välj den **tunnelbane** plats där du vill konfigurera peering.

        > [!NOTE]
        > Om du redan har peering-anslutningar med Microsoft på den valda platsen för **tunnelbane linje** och du använder portalen för första gången för att konfigurera peering på den platsen, visas dina befintliga peering-anslutningar i avsnittet **peering Connections** (se nedan). Microsoft kommer automatiskt att konvertera peering-anslutningarna till Azure-resursen så att du kan hantera dem tillsammans med de nya anslutningarna på ett och samma ställe. Se [konvertera en äldre Exchange-peering till Azure-resursen med hjälp av portalen](../howto-legacy-exchange-portal.md) för mer information.
        >

1. Under **peering-anslutningar**klickar du på **Skapa ny** för att lägga till en rad för varje ny anslutning som du vill konfigurera.

    * Om du vill konfigurera/ändra anslutnings inställningar klickar du på knappen Redigera för en rad.

        > [!div class="mx-imgBorder"]
        > ![peering-konfiguration – redigerings](../media/setup-exchange-conf-tab-edit.png) för Exchange

    * Om du vill ta bort en rad klickar du på **...** knappen > **ta bort**.

        > [!div class="mx-imgBorder"]
        > ![peering-konfiguration – redigerings](../media/setup-exchange-conf-tab-delete.png) för Exchange

    * Du måste ange alla inställningar för en anslutning som visas nedan.

         > [!div class="mx-imgBorder"]
         > ![peering-konfiguration – Exchange-anslutning](../media/setup-exchange-conf-tab-connection.png)

        1. Välj **peering-funktionen** där anslutningen måste konfigureras.
        1. I fälten **IPv4-adress** och **IPv6-adress**anger du IPv4-och IPv6-adresser som ska konfigureras i Microsoft-routrar med hjälp av Neighbor-kommandot.
        1. Ange antalet IPv4-och IPv6-prefix som du kommer att annonsera i fälten **maximalt antal annonserade IPv4-adresser** och **högsta antal annonserade IPv6-adresser** .
        1. Spara anslutnings inställningarna genom att klicka på **OK** .

1. Upprepa steget ovan om du vill lägga till fler anslutningar på alla anläggningar där Microsoft samverkar med ditt nätverk, inom den **tunnelbane** som du valde tidigare.

1. När du har lagt till alla nödvändiga anslutningar klickar du på **Granska + skapa**.

    > [!div class="mx-imgBorder"]
    > Fliken ![-peering-konfiguration slutlig](../media/setup-exchange-conf-tab-final.png)

1. Observera att portalen kör grundläggande verifiering av den information som du har angett. Detta visas i ett menyfliksområde överst, som *körning av slutgiltig verifiering...* .

    > [!div class="mx-imgBorder"]
    > Fliken ![-peering-verifiering](../media/setup-direct-review-tab-validation.png)

1. När den har *blivit klar med verifieringen*verifierar du din information och skickar in begäran genom att klicka på **skapa**. Om du behöver ändra din begäran klickar du på **föregående** och upprepar stegen ovan.

    > [!div class="mx-imgBorder"]
    > ![peering-sändning](../media/setup-exchange-review-tab-submit.png)

1. När du har skickat begäran väntar du tills den har slutfört distributionen. Om distributionen Miss lyckas, kontakta [Microsoft-peering](mailto:peering@microsoft.com). En lyckad distribution visas som nedan.

    > [!div class="mx-imgBorder"]
    > ![-peering lyckades](../media/setup-direct-success.png)
