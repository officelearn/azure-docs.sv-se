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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678526"
---
1. Välj **skapa en resurs** > **Se alla**.

    > [!div class="mx-imgBorder"]
    > ![Sök peering](../media/setup-seeall.png)

1. Sök efter **peering** i sökrutan och välj **RETUR** på tangent bordet. Välj en **peering** -resurs från resultaten.

    > [!div class="mx-imgBorder"]
    > ![Starta peering](../media/setup-launch.png)

1. När **peering** startar granskar du sidan för att förstå detaljerna. När du är klar väljer du **skapa**.

    > [!div class="mx-imgBorder"]
    > ![Skapa peering](../media/setup-create.png)

1. På sidan **skapa en peering** , på fliken **grundläggande** , fyller du i rutorna som visas här.

    > [!div class="mx-imgBorder"]
    > ![Fliken peering-grundläggande](../media/setup-basics-tab.png)

    * Välj din Azure- **prenumeration**.
    * För **resurs grupp**kan du antingen välja en befintlig resurs grupp i list rutan eller skapa en ny grupp genom att välja **Skapa ny**. Vi ska skapa en ny resurs grupp för det här exemplet.
    * **Namnet** motsvarar resurs namnet och kan vara allt du väljer.
    * **Regionen** markeras autoom du väljer en befintlig resurs grupp. Om du väljer att skapa en ny resurs grupp måste du också välja den Azure-region där du vill att resursen ska finnas.

        > [!NOTE]
        > Den region där en resurs grupp finns är oberoende av den plats där du vill skapa peering med Microsoft. Men det är en bra idé att organisera dina peering-resurser i resurs grupper som finns i de närmaste Azure-regionerna. För peering i Ashburn kan du till exempel skapa en resurs grupp i USA, östra eller östra 2; USA.

    * Välj ditt ASN i rutan **peer-ASN** .

        > [!IMPORTANT]
        > * Du kan bara välja ett ASN med ValidationState som godkänd innan du skickar en peering-begäran. Om du precis skickat in din PeerAsn-begäran, vänta i 12 timmar eller så att ASN-associationen kan godkännas. Om det ASN du väljer är väntande verifiering visas ett fel meddelande. 
        > * Om du inte ser det ASN du behöver välja, kontrollerar du att du har valt rätt prenumeration. Om så är fallet kontrollerar du om du redan har skapat PeerAsn genom att använda [associera peer-ASN till Azure-prenumerationen](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Grunderna om peering är fyllda](../media/setup-direct-basics-filled-tab.png)

    * Välj **Nästa: konfigurations >** för att fortsätta.
