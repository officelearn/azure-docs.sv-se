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
ms.openlocfilehash: 4bcee1097010bb8746b11185a470ca2584485c3f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488981"
---
1. På Portal sidan för ditt virtuella WAN-nätverk går du till avsnittet **anslutning** och väljer **VPN-platser** för att öppna sidan VPN-platser.
2. På sidan **VPN-platser** klickar du på **+ Skapa webbplats**.

   ![Grundläggande inställningar](./media/virtual-wan-tutorial-site-include/basics.png "Grundläggande inställningar")
3. På sidan **skapa VPN-webbplats** går du till fliken **grundläggande** och fyller i följande fält:

    * **Region** – tidigare kallad plats. Det här är den plats där du vill skapa den här webbplats resursen i.
    * **Namn** – namnet som du vill referera till din lokala plats.
    * **Enhets leverantör** – namnet på VPN-enhetens tillverkare (till exempel: Citrix, Cisco, Barracuda). Detta kan hjälpa Azure-teamet att bättre förstå din miljö för att lägga till ytterligare optimerings möjligheter i framtiden eller för att hjälpa dig att felsöka.
    * **Border Gateway Protocol** -Enable innebär att alla anslutningar från platsen kommer att vara BGP-aktiverade. Du kommer att behöva konfigurera BGP-informationen för varje länk från VPN-platsen i avsnittet länkar. Konfiguration av BGP på ett virtuellt WAN-nätverk motsvarar att konfigurera BGP på en virtuell Azure-nätverksgateway VPN. Din lokala BGP-peer-adress får inte vara samma som den offentliga IP-adressen för VPN-enheten eller det virtuella nätverkets adress utrymme för VPN-platsen. Använd en annan IP-adress på VPN-enheten för din BGP-peer-IP. Det kan vara en adress som tilldelats till loopback-gränssnittet på enheten. Det kan dock inte vara en APIPA-adress (169.254. x. x). Ange den här adressen på motsvarande VPN-plats som representerar platsen. För BGP-krav, se [om BGP med Azure VPN gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Du kan alltid redigera en VPN-anslutning för att uppdatera dess BGP-parametrar (peering IP-adress på länken och AS #) när inställningen för BGP-inställningen för VPN-platsen är aktive rad.
    * **Privat adress utrymme** – det IP-adressutrymme som finns på din lokala plats. Trafik till det här adressutrymmet dirigeras till den lokala platsen. Detta krävs när BGP inte är aktiverat för platsen.
    * **Hubbar** – hubben som du vill att din webbplats ska ansluta till. En plats kan bara anslutas till de hubbar som har en VPN Gateway. Om du inte ser någon hubb måste du först skapa en VPN-gateway i hubben.
4. Välj **länkar** om du vill lägga till information om de fysiska länkarna i grenen. Om du har en virtuell WAN-partner CPE-enhet, kontrollerar du med dem för att se om den här informationen utbyts med Azure som en del av överföringen av gren information från sina system.

   ![Länkar](./media/virtual-wan-tutorial-site-include/links.png "Länkar")

    * **Länknamn** – ett namn som du vill använda för den fysiska länken på VPN-platsen. Exempel: mylink1.
    * **Providernamn** – namnet på den fysiska länken på VPN-platsen. Exempel: till, Verizon.
    * **Hastighet** – detta är hastigheten på VPN-enheten på gren platsen. Exempel: 50, vilket innebär att 50 Mbit/s är hastigheten på VPN-enheten på gren platsen.
    * **IP-adress** – den offentliga IP-adressen för den lokal enheten med hjälp av den här länken. Alternativt kan du ange den privata IP-adressen för din lokala VPN-enhet som ligger bakom ExpressRoute.
5. Du kan använda kryss rutan för att ta bort eller lägga till ytterligare länkar. Det finns stöd för fyra länkar per VPN-webbplats. Om du till exempel har fyra ISP (Internet Service Provider) på gren platsen kan du skapa fyra länkar. en per Internet leverantör och ange informationen för varje länk.
6. När du är klar med att fylla i fälten väljer du **Granska + skapa** för att verifiera och skapa platsen.
7. Visa status på sidan VPN-webbplatser. Webbplatsen går till den **anslutning som krävs** eftersom platsen ännu inte har anslutits till hubben.