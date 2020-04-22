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
ms.openlocfilehash: 00c24212706555667ad4680c086ece24f15ca59a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678526"
---
1. Välj **Skapa en resurs** > **Se alla**.

    > [!div class="mx-imgBorder"]
    > ![Sök peering](../media/setup-seeall.png)

1. Sök efter **peering** i sökrutan och välj **Retur** på tangentbordet. Välj en **peering-resurs** i resultatet.

    > [!div class="mx-imgBorder"]
    > ![Starta peering](../media/setup-launch.png)

1. När **Peering** startar granskar du sidan för att förstå detaljerna. När du är klar väljer du **Skapa**.

    > [!div class="mx-imgBorder"]
    > ![Skapa peering](../media/setup-create.png)

1. På sidan **Skapa en peering** på fliken **Grunderna** fyller du i rutorna som visas här.

    > [!div class="mx-imgBorder"]
    > ![Fliken Peering Basics](../media/setup-basics-tab.png)

    * Välj din **Azure-prenumeration**.
    * För **resursgruppen**kan du antingen välja en befintlig resursgrupp i listrutan eller skapa en ny grupp genom att välja **Skapa ny**. Vi skapar en ny resursgrupp för det här exemplet.
    * **Namnet** motsvarar resursnamnet och kan vara vad du vill.
    * **Region** väljs automatiskt om du väljer en befintlig resursgrupp. Om du väljer att skapa en ny resursgrupp måste du också välja den Azure-region där du vill att resursen ska finnas.

        > [!NOTE]
        > Den region där en resursgrupp finns är oberoende av den plats där du vill skapa peering med Microsoft. Men det är en bra idé att organisera dina peering-resurser i resursgrupper som finns i de närmaste Azure-regionerna. För peerings i Ashburn kan du till exempel skapa en resursgrupp i östra USA eller östra US2.

    * Välj ASN i rutan **Peer ASN.**

        > [!IMPORTANT]
        > * Du kan bara välja ett ASN med ValidationState som godkänt innan du skickar en peering-begäran. Om du just har skickat in din PeerAsn-begäran väntar du i 12 timmar eller så på att ASN-associationen ska godkännas. Om det ASN du väljer väntar på validering visas ett felmeddelande. 
        > * Om du inte ser det ASN du behöver välja kontrollerar du att du har valt rätt prenumeration. Om så är fallet, kontrollera om du redan har skapat PeerAsn med hjälp av [Associera Peer ASN till Azure-prenumeration](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Peering Basics ifyllda](../media/setup-direct-basics-filled-tab.png)

    * Välj **Nästa : Konfiguration >** för att fortsätta.
