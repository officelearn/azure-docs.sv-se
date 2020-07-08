---
title: inkludera fil
titleSuffix: Azure
description: inkludera fil
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: e50f53c65c330265d3d9ec1b1804a7910b632b1d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81678822"
---
1. Gå till **resurs grupper**och välj den resurs grupp som du valde när du skapade **peering** -resursen. Använd **filter** rutan om du har för många resurs grupper.

    > [!div class="mx-imgBorder"]
    > ![Resursgrupper](../media/setup-direct-get-resourcegroup.png)

1. Välj den **peering** -resurs som du har skapat.

    > [!div class="mx-imgBorder"]
    > ![Vyn peering-resurs](../media/setup-direct-get-open.png)

1. På sidan **Översikt** visas information på hög nivå, som du ser här.

    > [!div class="mx-imgBorder"]
    > ![Översikts fönster över peering-resurs](../media/setup-direct-get-overview.png)

1. Till vänster väljer du **ASN-information** för att visa informationen som skickas när du skapade PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![ASN-information för peering-resurs](../media/setup-direct-get-asninfo.png)

1. Välj **anslutningar**till vänster. Längst upp på skärmen visas en sammanfattning av peering-anslutningar mellan ditt ASN och Microsoft, mellan olika anläggningar i Metro. Du kan också komma åt anslutnings sammanfattningen från **översikts** sidan genom att välja **anslutningar** i mitten av fönstret som visas.

    > [!div class="mx-imgBorder"]
    > ![Peering resurs anslutningar](../media/setup-direct-get-connectionssummary.png)

    * **Anslutnings status** motsvarar statusen för installationen av peering-anslutningen. De tillstånd som visas i det här fältet följer tillstånds diagrammet som visas i [genom gången av direkt peering](../walkthrough-direct-all.md).
    * Status för **IPv4-sessionstillstånd** och **IPv6-sessionstillstånd** motsvarar tillstånden för IPv4-och IPv6 BGP-sessionstillstånd. 
    * När du väljer en rad högst upp på skärmen visas information om varje anslutning i avsnittet **anslutning** längst ned. Välj pilarna för att expandera **konfigurationen**, **IPv4-adressen**och **IPv6-adressen**.
