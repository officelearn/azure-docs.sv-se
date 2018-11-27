---
title: Krav för Azure ExpressRoute-införande | Microsoft Docs
description: Den här sidan innehåller en lista med de krav som ska vara uppfyllda innan du kan beställa en Azure ExpressRoute-krets.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
ms.assetid: f872d25e-acfd-405d-9d1b-dcb9f323a2ff
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2018
ms.author: mialdrid
ms.openlocfilehash: 3e597e4f8be0af6e42f8ccc09c058cda619764a7
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713443"
---
# <a name="expressroute-prerequisites--checklist"></a>ExpressRoute-krav och checklista
Om du vill ansluta till Microsofts molntjänster med ExpressRoute måste du kontrollera att följande krav som anges i följande avsnitt är uppfyllda.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Azure-konto
* Ett giltigt och aktivt Microsoft Azure-konto. Detta konto är nödvändigt för att kunna konfigurera ExpressRoute-kretsen. ExpressRoute-kretsar är resurser i Azure-prenumerationer. Du måste ha en Azure-prenumeration, även om anslutningen är begränsad till Microsoft-molntjänster som inte finns i Azure, som till exempel Office 365-tjänster och Dynamics 365.
* En aktiv prenumeration på Office 365 (om du använder Office 365-tjänster). Mer information finns i [Särskilda krav för Office 365](#office-365-specific-requirements) i den här artikeln.

## <a name="connectivity-provider"></a>Anslutningsleverantör

* Du kan samarbeta med en [ExpressRoute-anslutningspartner](expressroute-locations.md#partners) för att ansluta till Microsoft-molnet. Du kan konfigurera en anslutning mellan ditt lokala nätverk och Microsoft på [tre sätt](expressroute-introduction.md).
* Om din leverantör inte är en ExpressRoute-anslutningspartner kan du fortfarande ansluta till Microsoft-molnet via en [molnutbytesleverantör](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Nätverkskrav
* **Redundant anslutning**: Det krävs inte någon redundans på den fysiska anslutningen mellan dig och din leverantör. Microsoft kräver dock att redundanta BGP-sessioner konfigureras mellan Microsofts routrar och peeringroutrar, även om du bara har [en fysisk anslutning till ett molnutbyte](expressroute-faqs.md#onep2plink).
* **Routning**: Beroende på hur du ansluter till Microsoft Cloud måste du eller din leverantör konfigurera och hantera BGP-sessioner för [routningsdomäner](expressroute-circuit-peerings.md). Vissa Ethernet-anslutningsleverantörer eller molnutbytesleverantörer kan erbjuda BGP-hantering som en mervärdestjänst.
* **NAT**: Microsoft godkänner bara offentliga IP-adresser via Microsoft-peering. Om du använder privata IP-adresser i det lokala nätverket måste du eller din leverantör översätta dem till offentliga IP-adresser [med hjälp av NAT](expressroute-nat.md).
* **QoS**: Skype för företag har olika tjänster (till exempel: röst, video, text) som kräver särskild QoS-behandling. Du och din leverantör bör följa [QoS-kraven](expressroute-qos.md).
* **Nätverkssäkerhet**: Fundera över [nätverkssäkerheten](../best-practices-network-security.md) när du ansluter till Microsoft Cloud via ExpressRoute.

## <a name="office-365"></a>Office 365
Om du planerar att aktivera Office 365 på ExpressRoute bör du läsa följande dokument för att få mer information om kraven för Office 365.

* [Översikt över ExpressRoute för Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Routning med ExpressRoute för Office 365](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Hög tillgänglighet och redundans med ExpressRoute](https://aka.ms/erhighavailability)
* [URL:er och IP-adressintervall för Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Nätverksplanering och prestandajustering för Office 365](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Räknare och verktyg för nätverksbandbredd](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Office 365-integration med lokala miljöer](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [ExpressRoute på Office 365 – avancerade utbildningsvideor](https://channel9.msdn.com/series/aer/)

## <a name="dynamics-365"></a>Dynamics 365
Om du planerar att aktivera Dynamics 365 på ExpressRoute bör du läsa följande dokument för att få mer information om Dynamics 365

* [Dynamics 365 och ExpressRoute-dokumentation](https://download.microsoft.com/download/B/2/8/B2896B38-9832-417B-9836-9EF240C0A212/Microsoft%20Dynamics%20365%20and%20ExpressRoute.pdf)
* [Dynamics 365-URL:er](https://support.microsoft.com/kb/2655102) och [IP-adressintervall](https://support.microsoft.com/kb/2728473)

## <a name="next-steps"></a>Nästa steg
* Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
* Hitta en ExpressRoute-anslutningsleverantör. Se [ExpressRoute-partners och peeringplatser](expressroute-locations.md).
* Se kraven för [routning](expressroute-routing.md), [NAT](expressroute-nat.md) och [QoS](expressroute-qos.md).
* Konfigurera ExpressRoute-anslutningen.
  * [Skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md)
  * [Konfigurera routning](expressroute-howto-routing-arm.md)
  * [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-arm.md)
