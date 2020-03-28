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
ms.openlocfilehash: 8b338f25e9771f5947fd494cfb00d0f6cb9ef67a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75450846"
---
1. Välj **Anslut VPN-platser** för att öppna sidan **Anslut webbplatser.**

    ![connect](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "connect")

   Fyll i följande fält:

   * Ange en fördelad nyckel. Om du inte anger en nyckel genererar Azure automatiskt en åt dig.
   * Välj inställningar för protokoll och IPsec. Se [standard-/anpassad IPSec-information] (https://docs.microsoft.com/azure/virtual-wan/virtual-wan-ipsec)
   * Välj lämpligt alternativ för **Sprid standardväg**. Med alternativet **Aktivera** kan den virtuella hubben sprida en inlärd standardväg till den här anslutningen. Den här flaggan aktiverar standardrutbredning till en anslutning endast om standardvägen redan har lärts av den virtuella WAN-hubben som ett resultat av att en brandvägg distribueras i navet eller om en annan ansluten plats har aktiverat tunnel. Standardvägen kommer inte från hubben Virtuellt WAN.

2. Välj **Anslut**.
3. Om några minuter visar platsen anslutnings- och anslutningsstatus.

   ![status](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Anslutningsstatus:** Det här är statusen för Azure-resursen för anslutningen som ansluter VPN-platsen till Azure-hubbens VPN-gateway. När den här kontrollplansåtgärden har slutförts fortsätter Azure VPN-gatewayen och den lokala VPN-enheten att upprätta anslutningen.

   **Anslutningsstatus:** Det här är den faktiska anslutningsstatusen (datasökvägen) mellan Azures VPN-gateway i navet och VPN-platsen. Det kan visa något av följande tillstånd:

    * **Okänd**: Det här tillståndet visas vanligtvis om serverdsystemen arbetar för att övergå till en annan status.
    * **Ansluta:** Azure VPN-gateway försöker nå ut till den faktiska lokala VPN-platsen.
    * **Ansluten**: Anslutning upprättas mellan Azure VPN-gateway och lokal VPN-plats.
    * **Frånkopplad**: Den här statusen visas om anslutningen av någon anledning (lokalt eller i Azure) kopplades från.
4. Inom en vpn-plats i navet kan du dessutom göra följande: 

   * Redigera eller ta bort VPN-anslutningen.
   * Ta bort webbplatsen i Azure-portalen.
   * Hämta en grenspecifik konfiguration för information om Azure-sidan med hjälp av menyn kontext (...) bredvid webbplatsen. Om du vill hämta konfigurationen för alla anslutna platser i navet väljer du **Ladda ned VPN Config** på den övre menyn.
