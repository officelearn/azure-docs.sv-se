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
ms.openlocfilehash: e50f53c65c330265d3d9ec1b1804a7910b632b1d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678822"
---
1. Gå till **resursgrupper**och välj den resursgrupp som du valde när du skapade **peering-resursen.** Använd rutan **Filter** om du har för många resursgrupper.

    > [!div class="mx-imgBorder"]
    > ![Resursgrupper](../media/setup-direct-get-resourcegroup.png)

1. Välj den **peering-resurs** som du skapade.

    > [!div class="mx-imgBorder"]
    > ![Peering resursvy](../media/setup-direct-get-open.png)

1. På sidan **Översikt** visas information på hög nivå, som visas här.

    > [!div class="mx-imgBorder"]
    > ![Översiktsfönster för peering-resurser](../media/setup-direct-get-overview.png)

1. Till vänster väljer du **ASN-information** för att visa informationen som skickades när du skapade PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![ASN-information om peering-resurs](../media/setup-direct-get-asninfo.png)

1. Till vänster väljer du **Anslutningar**. Högst upp på skärmen visas en sammanfattning av peering-anslutningar mellan DITT ASN och Microsoft, över olika anläggningar inom tunnelbanan. Du kan också komma åt sammanfattningen av anslutningar från sidan **Översikt** genom att välja **Anslutningar** i mitten av fönstret, som visas.

    > [!div class="mx-imgBorder"]
    > ![Peering-resursanslutningar](../media/setup-direct-get-connectionssummary.png)

    * **Anslutningstillståndet** motsvarar tillståndet för peering-anslutningsinställningarna. De lägen som visas i det här fältet följer tillståndsdiagrammet som visas i [direkt peering-genomgången](../walkthrough-direct-all.md).
    * **IPv4-sessionstillståndet** och **IPv6-sessionstillståndet** motsvarar iPv4- respektive IPv6 BGP-sessionstillståndet. 
    * När du väljer en rad högst upp på skärmen visas information om information för varje anslutning i avsnittet **Anslutning** längst ned. Markera pilarna för att expandera **Configuration,** **IPv4-adress**och **IPv6-adress**.
