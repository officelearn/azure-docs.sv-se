---
title: ta med fil
titleSuffix: Azure
description: ta med fil
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: include
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 60752cf1b3c05ab7817083e70310ba7b40227dec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129966"
---
1. Klicka på en peering-anslutning som du vill aktivera för peering-tjänst och klicka sedan på **...**  >  **Redigera** anslutningsknappen.
    > [!div class="mx-imgBorder"]
    > ![Redigera peering-anslutning](../media/setup-direct-modify-editconnection.png)
1. Under avsnittet ***Använd för peering-tjänst***klickar du på **Aktiverad** och **Spara**.
    > [!div class="mx-imgBorder"]
    > ![Peering-anslutning aktivera peering-tjänst](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. På skärmen Översikt visas distributionsinformationen. När distributionen är klar klickar du på **Gå till resurs**.
    > [!div class="mx-imgBorder"]
    > ![Distributionen är slutförd](../media/setup-direct-modify-overview-deployment-complete.png)

1. Du kommer då att se under Inställningar **registrerade prefix**. Klicka på **Lägg till registrerat prefix**.
    > [!div class="mx-imgBorder"]
    > ![Registrerade prefix och anslutningar](../media/setup-direct-modify-add-registered-prefix.png)
1. Registrera ett prefix genom att välja ett **namn** och ett **prefix** och klicka på **Spara**
    > [!div class="mx-imgBorder"]
    >  ![Registrera ett prefix](../media/setup-direct-modify-register-a-prefix.png) 

1. När ett prefix har skapats visas det i listan över registrerade prefix. Klicka på **prefixets namn** för att se mer information.
    > [!div class="mx-imgBorder"]
    > ![Registrerade prefix och anslutningar](../media/setup-direct-modify-registered-prefixes.png)
1. På den registrerade prefixsidan ser du fullständig information för att inkludera **prefixnyckeln** för varje prefix. Den här nyckeln måste tillhandahållas kunden som tilldelats det här prefixet från sin leverantörsleverantör. Kunden kan sedan registrera sitt prefix i sin prenumeration med den här nyckeln.
    > [!div class="mx-imgBorder"]
    > ![Prefix med prefixnyckel](../media/setup-direct-modify-registered-prefix-detail.png)