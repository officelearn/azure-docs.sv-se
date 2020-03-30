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
ms.openlocfilehash: 2e8938f270ed175c687d975b0b248275ad92f8e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75775087"
---
1. Gå till **resursgrupper** och klicka på den resursgrupp du valde när du skapade **peering-resurs.** Du kan använda fältet *Filter* om du har för många resursgrupper.

    > [!div class="mx-imgBorder"]
    > ![Resursgrupp för peering](../media/setup-direct-get-resourcegroup.png)

1. Klicka på **peering-resursen** som du skapade.

    > [!div class="mx-imgBorder"]
    > ![Peering resursvy](../media/setup-direct-get-open.png)

1. På sidan **Översikt** visas information på hög nivå. Observera den information som markeras nedan.

    > [!div class="mx-imgBorder"]
    > ![Peering resursvy](../media/setup-direct-get-overview.png)

1. Till vänster klickar du på **ASN-information** för att visa information som lämnats när du skapar PeerAsn

    > [!div class="mx-imgBorder"]
    > ![Peering resursvy](../media/setup-direct-get-asninfo.png)

1. Till vänster klickar du på **Anslutningar**. Observera på toppen en sammanfattning av peering-anslutningar mellan ditt ASN och Microsoft, över olika anläggningar inom tunnelbanan. Du kan också komma fram till sammanfattningen av anslutningar från **översiktssidan** genom att klicka på **Anslutningar** i mittrutan som markerats ovan.

    > [!div class="mx-imgBorder"]
    > ![Peering resursvy](../media/setup-direct-get-connectionssummary.png)

    * **Anslutningstillståndet** motsvarar tillståndet för den peering-anslutning som ställts in. De lägen som visas i det här fältet följer tillståndsdiagrammet som visas i [direkt peer-genomgången](../walkthrough-direct-all.md)
    * **IPv4-sessionstillstånd** och **IPv6-sessionstillstånd** motsvarar iPv4- respektive IPv6 BGP-sessionstillståndet.  
    * När du väljer en rad överst visar avsnittet ***Anslutning*** längst ned information för varje anslutning. Du kan klicka på pilmarker för att expandera ***underavsnitten Konfiguration,*** ***IPv4-adress*** och ***IPv6-adress***
