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
ms.openlocfilehash: 79cf4b2edd1a25df427cf15f9ee7f2f331ebd2df
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774645"
---
1. Klicka på **skapa en resurs** > **Se alla**.

    > [!div class="mx-imgBorder"]
    > ![Sök peering](../media/setup-seeall.png)

1. Sök efter *peering* i sökrutan och tryck på *RETUR* på tangent bordet. Klicka på **peering** -resurs från resultaten.

    > [!div class="mx-imgBorder"]
    > ![starta peering](../media/setup-launch.png)

1. När **peering** har startats granskar du sidan för att förstå detaljerna. När du är klar klickar du på **skapa**.

    > [!div class="mx-imgBorder"]
    > ![skapa peering-](../media/setup-create.png)

1. På fliken **skapa en peering** , under fliken **grundläggande** , fyller du i fälten som visas nedan.

    > [!div class="mx-imgBorder"]
    > ![peering-grunderna](../media/setup-basics-tab.png)

    * Välj din Azure- **prenumeration**.
    * För **resurs grupp**kan du antingen välja en befintlig resurs grupp från listruta eller skapa en ny grupp genom att klicka på **Skapa ny**. Vi kommer att skapa en ny resurs grupp för det här exemplet.
    * **Namnet** motsvarar resurs namnet och kan vara allt du väljer.
    * **Regionen** väljs automatiskt om du väljer en befintlig resurs grupp i steget ovan. Om du väljer att skapa en ny resurs grupp måste du också välja den Azure-region där du vill att resursen ska finnas. USA, östra

        > [!NOTE]
        > Den region där resurs gruppen finns är oberoende av den plats där du vill skapa peering med Microsoft. Men det är bästa praxis att organisera dina peering-resurser i resurs grupper som finns i de närmaste Azure-regionerna. Tex: för peering i Ashburn kan du skapa en resurs grupp i *USA, östra* eller *östra 2; USA*

    * Välj ditt ASN i fältet **peer-ASN** .

        > [!IMPORTANT]
        > * Du kan bara välja ett ASN med ValidationState som "godkänt" innan du skickar en peering-begäran. Om du precis skickat in din PeerAsn-begäran, vänta i 12 timmar eller så att ASN-associationen blir "godkänd". Om det ASN du väljer är väntande verifiering visas ett fel meddelande. 
        > * Om du inte ser det ASN du behöver välja måste du kontrol lera att du har valt rätt prenumeration. Om så är fallet kontrollerar du om du redan har skapat PeerAsn med [associera peer-ASN till Azure-prenumerationen](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > Grunderna för ![peering, fyllda](../media/setup-direct-basics-filled-tab.png)

    * Klicka på **Nästa: konfigurations >** för att fortsätta.
