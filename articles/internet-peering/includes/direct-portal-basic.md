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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774645"
---
1. Klicka på **Skapa en resurs** > **Visa alla**.

    > [!div class="mx-imgBorder"]
    > ![Sök peering](../media/setup-seeall.png)

1. Sök efter *peering* i sökrutan och tryck på *Retur* på tangentbordet. Från resultaten klickar du på **Peering-resurs.**

    > [!div class="mx-imgBorder"]
    > ![Starta peering](../media/setup-launch.png)

1. När **Peering** har startats går du igenom sidan för att förstå detaljer. När du är klar klickar du på **Skapa**.

    > [!div class="mx-imgBorder"]
    > ![Skapa peering](../media/setup-create.png)

1. På sidan **Skapa en peering** under fliken **Grunderna** fyller du i fälten som visas nedan.

    > [!div class="mx-imgBorder"]
    > ![Grunderna i peering](../media/setup-basics-tab.png)

    * Välj din **Azure-prenumeration**.
    * För **resursgruppen**kan du antingen välja en befintlig resursgrupp från listrutan eller skapa en ny grupp genom att klicka på **Skapa ny**. Vi kommer att skapa en ny resursgrupp för det här exemplet.
    * **Namn** motsvarar resursnamn och kan vara vad du vill.
    * **Region** väljs automatiskt om du väljer en befintlig resursgrupp i steget ovan. Om du väljer att skapa en ny resursgrupp måste du också välja den Azure-region där du vill att resursen ska finnas. USA, östra

        > [!NOTE]
        > Region där resursgruppen finns är oberoende av den plats där du vill skapa peering med Microsoft. Men det är bäst att organisera dina peering-resurser i resursgrupper som finns i de närmaste Azure-regionerna. T.ex. för peerings i Ashburn kan du skapa en resursgrupp i *östra USA* eller *östra US2*

    * Välj ASN i fältet **Peer ASN.**

        > [!IMPORTANT]
        > * Du kan bara välja ett ASN med ValidationState som "Godkänd" innan du skickar en peering-begäran. Om du just har skickat in din PeerAsn-begäran väntar du i 12 timmar eller så på att ASN-associationen ska vara "Godkänd". Om det ASN du väljer väntar på validering visas ett felmeddelande. 
        > * Om du inte ser det ASN du behöver välja kontrollerar du om du har valt rätt prenumeration. Om så är fallet, kontrollera om du redan har skapat PeerAsn med [associera peer ASN till Azure-prenumeration](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Peering Basics fyllda](../media/setup-direct-basics-filled-tab.png)

    * Klicka på **Nästa: Konfiguration >** för att fortsätta.
