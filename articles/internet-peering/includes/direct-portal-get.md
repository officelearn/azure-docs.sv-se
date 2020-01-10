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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775087"
---
1. Gå till **resurs grupper** och klicka på den resurs grupp som du valde när du skapade **peering** -resursen. Du kan använda *filter* fältet om du har för många resurs grupper.

    > [!div class="mx-imgBorder"]
    > ![peering-resurs grupp](../media/setup-direct-get-resourcegroup.png)

1. Klicka på **peering** -resursen som du skapade.

    > [!div class="mx-imgBorder"]
    > ![peering Resource View](../media/setup-direct-get-open.png)

1. På sidan **Översikt** visas information på hög nivå. Observera informationen som marker ATS nedan.

    > [!div class="mx-imgBorder"]
    > ![peering Resource View](../media/setup-direct-get-overview.png)

1. Till vänster klickar du på **ASN-information** för att visa information som skickats när du skapar PeerAsn

    > [!div class="mx-imgBorder"]
    > ![peering Resource View](../media/setup-direct-get-asninfo.png)

1. Klicka på **anslutningar**till vänster. Observera den övre sammanfattningen av peering-anslutningar mellan ditt ASN och Microsoft, mellan olika anläggningar i Metro. Du kan också komma till översikts sidan för anslutningar från **översikten** genom att klicka på **anslutningar** i mitten av fönstret som är markerat ovan.

    > [!div class="mx-imgBorder"]
    > ![peering Resource View](../media/setup-direct-get-connectionssummary.png)

    * **Anslutnings statusen** motsvarar statusen för peering-anslutningens konfiguration. De tillstånd som visas i det här fältet följer tillstånds diagrammet som visas i [genom gången av direkt peering](../walkthrough-direct-all.md)
    * Tillstånd för **IPv4-sessionstillstånd** och **IPv6-sessionstillstånd** motsvarar IPv4-och IPv6 BGP-sessionstillstånd.  
    * När du väljer en rad högst upp visas information om varje anslutning i avsnittet ***anslutning*** längst ned. Du kan klicka på piltangenterna för att expandera under avsnitts ***konfigurationen***, ***IPv4-adressen*** och ***IPv6-adressen***
