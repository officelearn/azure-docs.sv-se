---
title: inkludera fil
description: inkludera fil
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8b338f25e9771f5947fd494cfb00d0f6cb9ef67a
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2020
ms.locfileid: "75450846"
---
1. Välj **Anslut VPN-platser** för att öppna sidan **Anslut platser** .

    ![gå](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "connect")

   Fyll i följande fält:

   * Ange en i förväg delad nyckel. Om du inte anger någon nyckel genererar Azure automatiskt en åt dig.
   * Välj protokoll-och IPsec-inställningar. Se [information om standard/anpassad IPSec] (https://docs.microsoft.com/azure/virtual-wan/virtual-wan-ipsec)
   * Välj lämpligt alternativ för att **sprida standard väg**. Alternativet **Aktivera** gör det möjligt för den virtuella hubben att sprida en inlärd standard väg till den här anslutningen. Den här flaggan aktiverar standard vägs spridning enbart till en anslutning om standard vägen redan har belärts av den virtuella WAN-hubben på grund av distribution av en brand vägg i hubben, eller om en annan ansluten plats har Tvingad tunnel trafik aktive rad. Standard vägen kommer inte från den virtuella WAN-hubben.

2. Välj **Anslut**.
3. Om några minuter visas anslutningen och anslutnings statusen för platsen.

   ![statusfältet](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Anslutnings status:** Detta är statusen för Azure-resursen för den anslutning som ansluter VPN-platsen till Azure Hub-VPN-gatewayen. När den här kontroll Plans åtgärden lyckas fortsätter Azure VPN-gatewayen och den lokala VPN-enheten att upprätta anslutningen.

   **Anslutnings status:** Detta är den faktiska anslutnings statusen (data Sök vägen) mellan Azures VPN-gateway på NAV-och VPN-platsen. Det kan visa något av följande tillstånd:

    * **Okänd**: det här tillståndet visas vanligt vis om backend-systemen arbetar för att övergå till en annan status.
    * **Ansluter**: Azure VPN-gatewayen försöker kontakta den faktiska lokala VPN-platsen.
    * **Ansluten**: anslutningen upprättas mellan Azure VPN-gatewayen och den lokala VPN-platsen.
    * **Frånkopplad**: den här statusen visas, oavsett orsak (lokalt eller i Azure), anslutningen kopplades från.
4. På en hubb VPN-webbplats kan du göra följande: 

   * Redigera eller ta bort VPN-anslutningen.
   * Ta bort platsen i Azure Portal.
   * Hämta en Branch-/regionsspecifika konfiguration för information om Azure-sidan med hjälp av kontexten (...) bredvid webbplatsen. Om du vill ladda ned konfigurationen för alla anslutna platser i hubben väljer du **Hämta VPN-** konfiguration på den översta menyn.
