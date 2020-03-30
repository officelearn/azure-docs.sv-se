---
title: Azure Virtual WAN och fjärrbearbetning
description: På den här sidan beskrivs hur du kan utnyttja Azure Virtual WAN för att möjliggöra fjärrarbete på grund av COVID-19-pandemin.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: article
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: ce212b5da90906966025674b58884d0e2f5bb064
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337128"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Azure Virtual WAN och stöd för fjärrarbete

>[!NOTE]
>I den här artikeln beskrivs hur du kan utnyttja Azure Virtual WAN, Azure, Microsoft-nätverket och Azure-partnerekosystemet för att arbeta på distans och minska nätverksproblem som du står inför på grund av COVID-19-krisen.
>

Är du klättra för att ge anslutning för fjärranvändare?
Ser du plötsligt ett behov av att stödja en våg av användare utöver vad du hade planerat för?
Behöver du användaren att ansluta hemifrån och inte bara komma åt molnet utan också kunna nå lokalt?
Behöver du dina fjärranvändare för att kunna nå resurser bakom ett privat WAN?
Har du ett behov av att användarna får åtkomst till molnresurser inom molnet utan att behöva konfigurera anslutning mellan regioner?
Eftersom den här globala pandemin skapar aldrig tidigare skådade förändringar omkring oss, finns Azure Virtual WAN-teamet här för att tillgodose dina anslutningsbehov.

Azure Virtual WAN är en nätverkstjänst som sammanför många nätverks-, säkerhets- och routningsfunktioner för att tillhandahålla ett enda operativt gränssnitt. Dessa funktioner inkluderar Branch-anslutning (via anslutningsautomatisering från Virtuella WAN-partnerenheter som SD-WAN eller VPN CPE), VPN-anslutning mellan plats och plats, ANSLUTNING mellan slutanvändare (Punkt-till-plats), Privat (ExpressRoute)-anslutning, Intra molnanslutning (Transitiv anslutning för virtuella nätverk), VPN ExpressRoute Interconnectivity, Routing, Azure-brandvägg, Kryptering för privat anslutning etc. Du behöver inte ha alla dessa användningsfall för att börja använda Virtual WAN. Du kan komma igång med bara ett användningsfall och justera ditt nätverk när det utvecklas.

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan1.png)

Nu talar om fjärranvändare, kan titta på vad du behöver för att få ditt nätverk igång:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Konfigurera fjärranvändaranslutning

Du kan ansluta till dina resurser i Azure via en IPsec/IKE (IKEv2) eller OpenVPN-anslutning. Den här typen av anslutning kräver att en VPN-klient konfigureras för fjärranvändaren. Den här klienten kan vara [Azure VPN-klienten](https://go.microsoft.com/fwlink/?linkid=2117554) eller OpenVPN-klienten eller en klient som stöder IKEv2. Mer information finns i [Skapa en punkt-till-plats-anslutning](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Anslutning från fjärranvändaren till lokalt

Du har två alternativ här:

* Konfigurera anslutning från plats till plats med valfri befintlig VPN-enhet. När du ansluter IPsec VPN-enheten till Azure Virtual WAN-hubb är sammankopplingen mellan VPN -vpn (point-to-site User VPN) och Plats-till-plats-VPN automatisk. Mer information om hur du konfigurerar Plats-till-plats-VPN från din lokala VPN-enhet till Azure Virtual WAN finns i [Skapa en anslutning från plats till plats med Virtual WAN](virtual-wan-site-to-site-portal.md).

* Anslut expressroutekretsen till Virtual WAN-hubben. För att ansluta en ExpressRoute-krets måste du distribuera en ExpressRoute-gateway i Virtuell WAN. Så snart du har distribuerat en, samtrafik mellan Point-to-site User VPN och ExpressRoute användaren är automatisk. Om du vill skapa ExpressRoute-anslutningen finns i [Skapa en ExpressRoute-anslutning med Virtual WAN](virtual-wan-expressroute-portal.md). Du kan använda en befintlig ExpressRoute-krets för att ansluta till Azure Virtual WAN.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Befintlig grundläggande Virtual WAN-kund

Grundläggande Virtuellt WAN tillhandahåller endast VPN från plats till plats. För att fjärranvändare ska kunna ansluta måste du uppgradera det virtuella WAN till Standard Virtual WAN. Steg för att uppgradera ett virtuellt WAN finns i [Uppgradera ett virtuellt WAN från Basic till Standard](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>Ytterligare information

Virtual WAN stöder en hubb per region/plats. Platsinformation finns i artikeln [Virtuella WAN-partner och platser.](virtual-wan-locations-partners.md) Varje hubb stöder upp till 10 000 fjärranvändaranslutningar, 1 000 filialanslutningar, fyra ExpressRoute-kretsar och upp till 500 virtuella nätverksanslutningar. När du skala upp fjärranvändare, om du har några frågor, tveka inte azurevirtualwan@microsoft.comatt söka hjälp genom att skicka ett mail till . Om du behöver teknisk support måste du öppna en supportbiljett från Azure-portalen och hjälpen är på väg.

## <a name="faq"></a><a name="faq"></a>Faq

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Efterföljande moment

Mer information om Virtuellt WAN finns i [Översikt över Virtuellt WAN](virtual-wan-about.md)