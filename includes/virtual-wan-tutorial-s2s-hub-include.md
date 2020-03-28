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
ms.openlocfilehash: 3bd9489adaf46e604393fc7059d37443bdd5ec3e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73488906"
---
1. Leta reda på det virtuella WAN som du skapade. Välj **Hubbar**under **avsnittet Anslutning** på sidan Virtuellt WAN .
2. På sidan Hubbar väljer du **+Ny hubb** för att öppna sidan **Skapa virtuellt nav.**

    ![Grundläggande inställningar](./media/virtual-wan-tutorial-hub-include/basics.png "Grundläggande inställningar")
3. Fyll i följande fält på fliken **Skapa virtuell hubbsida** **Grundläggande:**

    **Projektinformation**

   * Region (tidigare kallad plats)
   * Namn
   * Hub privat adressutrymme. Minsta adressutrymme är /24 för att skapa ett nav, vilket innebär att allt intervall från /25 till /32 kommer att skapa ett fel när du skapar.
4. Välj **Nästa: Plats-till-plats**.

    ![Plats till webbplats](./media/virtual-wan-tutorial-hub-include/site-to-site.png "Plats-till-plats")

5. Fyll i följande fält på fliken **Plats till plats:**

   * Välj **Ja** om du vill skapa ett VPN från plats till plats.
   * Fältet AS-nummer kan inte redigeras i den virtuella hubben just nu.
   * Välj värdet **för gatewayskalningsenheter** i listrutan. Med skalningsenheten kan du välja det sammanlagda dataflödet för VPN-gatewayen som skapas i det virtuella navet för att ansluta platser till. Om du väljer 1 skalenhet = 500 Mbit/s innebär det att två instanser för redundans skapas, var och en med ett maximalt dataflöde på 500 Mbit/s. Om du till exempel hade fem grenar, som var och en gör 10 Mbit/s på grenen, behöver du sammanlagt 50 Mbit/s i huvudänden. Planering för aggregerad kapacitet för Azure VPN-gatewayen bör göras efter att ha bedömt den kapacitet som behövs för att stödja antalet grenar till navet.
6. Välj **Granska + Skapa** för att validera.
7. Välj **Skapa** om du vill skapa navet. Efter 30 minuter **uppdaterar du** om du vill visa navet på **hubbarsidan.** Välj **Gå till resurs** för att navigera till resursen.