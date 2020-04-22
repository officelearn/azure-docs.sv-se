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
ms.openlocfilehash: e5804aa1b005e670d8b430b1c0a3bd62efd0bb06
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687061"
---
1. Välj en peering-anslutning som du vill aktivera för Azure Peering Service. Välj sedan **...**  >  **Redigera anslutning**.
    > [!div class="mx-imgBorder"]
    > ![Peering-anslutning Redigera anslutning](../media/setup-direct-modify-editconnection.png)
1. Under **Använd för peering-tjänst**väljer du **Aktiverad** och väljer sedan **Spara**.
    > [!div class="mx-imgBorder"]
    > ![Peering-anslutning Aktivera peering-tjänst](../media/setup-direct-modify-editconnectionsettings-peering-service.png)
1. På **översiktsskärmen** visas distributionsinformationen. När distributionen är klar väljer du **Gå till resurs**.
    > [!div class="mx-imgBorder"]
    > ![Distributionen är klar](../media/setup-direct-modify-overview-deployment-complete.png)

1. Välj **Lägg till registrerat prefix**i fönstret **Registrerade prefix** .
    > [!div class="mx-imgBorder"]
    > ![Lägg till registrerat prefix](../media/setup-direct-modify-add-registered-prefix.png)
1. Registrera ett prefix genom att välja ett **namn** och ett **prefix** och välja **Spara**.
    > [!div class="mx-imgBorder"]
    >  ![Registrera ett prefix](../media/setup-direct-modify-register-a-prefix.png) 

1. När ett prefix har skapats visas det i listan över **registrerade prefix**. Välj **namnet** på prefixet om du vill se mer information.
    > [!div class="mx-imgBorder"]
    > ![Registrerade prefix och anslutningar](../media/setup-direct-modify-registered-prefixes.png)
1. På den registrerade prefixsidan ser du fullständig information som innehåller **prefixnyckeln** för varje prefix. Den här nyckeln måste anges till kunden som tilldelats det här prefixet från sin leverantörsleverantör. Kunden kan sedan registrera sitt prefix i sin prenumeration med hjälp av den här nyckeln.
    > [!div class="mx-imgBorder"]
    > ![Prefix med prefixnyckel](../media/setup-direct-modify-registered-prefix-detail.png)