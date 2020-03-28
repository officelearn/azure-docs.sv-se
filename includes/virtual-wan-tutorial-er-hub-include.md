---
title: ta med fil
description: ta med fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6f910dbe91ed8e1cb65eefa6dfc48c72a689bf25
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73491454"
---
1. Leta reda på det virtuella WAN som du skapade. Välj **Hubbar**under **avsnittet Anslutning** på sidan Virtuellt WAN .
2. På sidan Hubbar väljer du **+Ny hubb** för att öppna sidan **Skapa virtuellt nav.**
3. Fyll i följande fält på fliken **Skapa virtuell hubbsida** **Grundläggande:**

   ![Grundläggande inställningar](./media/virtual-wan-tutorial-er-hub-include/hub1.png "Grundläggande inställningar")

    **Projektinformation**

   * Region (tidigare kallad plats)
   * Namn
   * Hub privat adressutrymme. Minsta adressutrymme är /24 för att skapa ett nav, vilket innebär att allt intervall från /25 till /32 kommer att skapa ett fel när du skapar.
4. Välj **fliken ExpressRoute**.

5. Fyll i följande fält på fliken **ExpressRoute:**

   ![ExpressRoute (ort)](./media/virtual-wan-tutorial-er-hub-include/hub2.png "ExpressRoute")

   * Välj **Ja** om du vill skapa en **ExpressRoute-gateway.**
   * Välj värdet **för gatewayskalningsenheter** i listrutan.
6. Välj **Granska + Skapa** för att validera.
7. Välj **Skapa** om du vill skapa navet. Efter 30 minuter **uppdaterar du** om du vill visa navet på **hubbarsidan.** Välj **Gå till resurs** för att navigera till resursen.