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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774502"
---
1. På sidan **Skapa en peering** under **fliken Konfiguration** fyller du i fälten som visas nedan.

    > [!div class="mx-imgBorder"]
    > ![Peering-konfiguration - Exchange](../media/setup-exchange-conf-tab.png)

    * För **peering-typ**väljer du *Exchange*.
    * Välj **SKU** som *Basic Free*.
    * Välj **Metro-platsen** för den plats där du vill konfigurera peering.

        > [!NOTE]
        > Om du redan har peering-anslutningar med Microsoft på den valda **Metro-platsen** och du använder portalen för första gången för att konfigurera peering på den platsen, visas dina befintliga peering-anslutningar i avsnittet **Peering-anslutningar** enligt nedan. Microsoft konverterar automatiskt dessa peering-anslutningar till Azure-resursen så att du kan hantera dem alla tillsammans med de nya anslutningarna på ett ställe. Mer information [finns i Konvertera en äldre Exchange-peering till Azure-resurs med hjälp av portalen.](../howto-legacy-exchange-portal.md)
        >

1. Under **Peering-anslutningar**klickar du på **Skapa ny** om du vill lägga till en rad för varje ny anslutning som du vill konfigurera.

    * Om du vill konfigurera/ändra anslutningsinställningar klickar du på redigeringsknappen för en rad.

        > [!div class="mx-imgBorder"]
        > ![Peering-konfiguration - Exchange-redigering](../media/setup-exchange-conf-tab-edit.png)

    * Om du vill ta bort en rad klickar du på **...-knappen** > **Ta bort**.

        > [!div class="mx-imgBorder"]
        > ![Peering-konfiguration - Exchange-redigering](../media/setup-exchange-conf-tab-delete.png)

    * Du måste ange alla inställningar för en anslutning som visas nedan.

         > [!div class="mx-imgBorder"]
         > ![Peering-konfiguration - Exchange-anslutning](../media/setup-exchange-conf-tab-connection.png)

        1. Välj den **peering-anläggning** där anslutningen måste ställas in.
        1. I fälten **IPv4-adress** och **IPv6-adress**anger du IPv4 respektive IPv6-adress som skulle konfigureras i Microsoft-routrar med kommandot neighbor.
        1. Ange antalet IPv4- och IPv6-prefix som du ska annonsera i fälten **Maximum advertised IPv4-adresser** respektive **Maximum advertised IPv6-adresser.**
        1. Spara anslutningsinställningarna genom att klicka på **OK.**

1. Upprepa ovanstående steg för att lägga till fler anslutningar på alla anläggningar där Microsoft är samlokaliseras med ditt nätverk, inom den **Metro** som valts tidigare.

1. När du har lagt till alla nödvändiga anslutningar klickar du på **Granska + skapa**.

    > [!div class="mx-imgBorder"]
    > ![Fliken Peering-konfiguration slutlig](../media/setup-exchange-conf-tab-final.png)

1. Observera att portalen kör grundläggande validering av den information du angav. Detta visas i ett band på toppen, som *Kör slutlig validering...*.

    > [!div class="mx-imgBorder"]
    > ![Fliken Validering av peering](../media/setup-direct-review-tab-validation.png)

1. När den har överförts till *Godkänd validering*verifierar du din information och skickar begäran genom att klicka på **Skapa**. Om du behöver ändra din begäran klickar du på **Föregående** och upprepar stegen ovan.

    > [!div class="mx-imgBorder"]
    > ![Skicka in peering](../media/setup-exchange-review-tab-submit.png)

1. När du har skickat begäran väntar du tills den har slutfört distributionen. Om distributionen misslyckas kontaktar du [Microsoft peering](mailto:peering@microsoft.com). En lyckad distribution visas enligt nedan.

    > [!div class="mx-imgBorder"]
    > ![Peering framgång](../media/setup-direct-success.png)
