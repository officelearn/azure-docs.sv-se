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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73488981"
---
1. På portalsidan för din virtuella wan väljer du **VPN-platser** i avsnittet **Anslutning** för att öppna sidan VPN-platser.
2. På sidan **VPN-platser** klickar du på **+ Skapa webbplats**.

   ![Grundläggande inställningar](./media/virtual-wan-tutorial-site-include/basics.png "Grundläggande inställningar")
3. Fyll i följande fält på fliken Skapa VPN-webbplats på sidan **Skapa** **VPN-webbplats:**

    * **Region** - Tidigare kallad plats. Det här är den plats som du vill skapa den här platsresursen i.
    * **Namn** - Det namn som du vill referera till din lokala webbplats.
    * **Enhetsleverantör** - Namnet på VPN-enhetsleverantören (till exempel: Citrix, Cisco, Barracuda). Om du gör det kan Azure-teamet bättre förstå din miljö för att lägga till ytterligare optimeringsmöjligheter i framtiden eller för att hjälpa dig att felsöka.
    * **Border Gateway Protocol** - Enable innebär att alla anslutningar från platsen kommer att bgp-aktiveras. Du kommer så småningom att ställa in BGP-informationen för varje länk från VPN-platsen i avsnittet Länkar. Konfigurera BGP på ett virtuellt WAN motsvarar att konfigurera BGP på en Azure virtuell nätverksgateway VPN. Din lokala BGP-peer-adress får inte vara samma som den offentliga IP-adressen för din VPN till enheten eller VPN-adressutrymmet på VPN-webbplatsen. Använd en annan IP-adress på VPN-enheten för din BGP-peer-IP. Det kan vara en adress som tilldelats till loopback-gränssnittet på enheten. Det kan dock inte vara en APIPA-adress (169.254.x.x). Ange den här adressen på motsvarande VPN-plats som representerar platsen. För BGP-förutsättningar finns i [Om BGP med Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Du kan alltid redigera en VPN-anslutning för att uppdatera dess BGP-parametrar (Peering IP på länken och AS #) när BGP-inställningen för VPN-platsen är aktiverad.
    * **Privat adressutrymme** - IP-adressutrymmet som finns på din lokala webbplats. Trafik till det här adressutrymmet dirigeras till den lokala platsen. Detta krävs när BGP inte är aktiverat för platsen.
    * **Hubbar** - Det nav som du vill att webbplatsen ska ansluta till. En plats kan bara anslutas till de hubbar som har en VPN-gateway. Om du inte ser ett nav, skapa en VPN-gateway i den hubben först.
4. Välj **Länkar** om du vill lägga till information om de fysiska länkarna på grenen. Om du har en CPE-enhet för virtuell wan-partner kontaktar du dem för att se om den här informationen utbyts med Azure som en del av överföringen av filialinformation som konfigurerats från deras system.

   ![Länkar](./media/virtual-wan-tutorial-site-include/links.png "Länkar")

    * **Länknamn** - Ett namn som du vill ange för den fysiska länken på VPN-platsen. Exempel: mylink1.
    * **Providernamn** - Namnet på den fysiska länken på VPN-platsen. Exempel: ATT, Verizon.
    * **Hastighet** - Detta är hastigheten på VPN-enheten på filialen plats. Exempel: 50, vilket innebär att 50 Mbps är hastigheten på VPN-enheten på filialplatsen.
    * **IP-adress** - Offentlig IP-adress för on-prem-enheten med den här länken. Du kan också ange den privata IP-adressen för din lokala VPN-enhet som finns bakom ExpressRoute.
5. Du kan använda kryssrutan för att ta bort eller lägga till ytterligare länkar. Fyra länkar per VPN-webbplats stöds. Om du till exempel har fyra Internet-leverantörer på filialplatsen kan du skapa fyra länkar. en per isp och ange information för varje länk.
6. När du har fyllt i fälten väljer du **Granska + skapa** för att verifiera och skapa webbplatsen.
7. Visa status på sidan VPN-webbplatser. Platsen går till **Anslutning behövs** eftersom platsen ännu inte har anslutits till navet.