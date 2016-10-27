<properties
   pageTitle="Krav för ExpressRoute-införande | Microsoft Azure"
   description="Den här sidan innehåller en lista med de krav som ska vara uppfyllda innan du kan beställa en Azure ExpressRoute-krets."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/10/2016"
   ms.author="cherylmc"/>



# ExpressRoute-krav och checklista  

Om du vill ansluta till Microsofts molntjänster med ExpressRoute måste du kontrollera att följande krav som anges i avsnitten nedan är uppfyllda.

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## Azure-konto

- Ett giltigt och aktivt Microsoft Azure-konto. Detta är nödvändigt för att kunna konfigurera ExpressRoute-kretsen. ExpressRoute-kretsar är resurser i Azure-prenumerationer. En Azure-prenumeration är ett krav, även om anslutningen är begränsad till Microsoft-molntjänster som inte finns i Azure, till exempel Office 365-tjänster och CRM online.
- En aktiv prenumeration på Office 365 (om du använder Office 365-tjänster). Se [Särskilda krav för Office 365](#office-365-specific-requirements) i den här artikeln för mer information.

## Anslutningsleverantör
- Du kan samarbeta med en [ExpressRoute-anslutningspartner](expressroute-locations.md#partners) för att ansluta till Microsoft-molnet. Du kan konfigurera en anslutning mellan ditt lokala nätverk och Microsoft på [tre sätt](expressroute-introduction.md#howtoconnect). 
- Om din leverantör inte är en ExpressRoute-anslutningspartner kan du fortfarande ansluta till Microsoft-molnet via en [molnutbytesleverantör](expressroute-locations.md#nonpartners).

## Nätverkskrav
- **Redundant anslutning**: Det krävs inte någon redundans på den fysiska anslutningen mellan dig och din leverantör. Microsoft kräver dock att redundanta BGP-sessioner konfigureras mellan Microsofts routrar och peeringroutrar, även om du bara har [en fysisk anslutning till ett molnutbyte](expressroute-faqs.md#onep2plink). 
- **Routning**: Beroende på hur du ansluter till Microsoft Cloud måste du eller din leverantör konfigurera och hantera BGP-sessioner för [routningsdomäner](expressroute-circuit-peerings.md). Vissa Ethernet-anslutningsleverantörer eller molnutbytesleverantörer kan erbjuda BGP-hantering som en mervärdestjänst.
- **NAT**: Microsoft godkänner bara offentliga IP-adresser via Microsoft-peering. Om du använder privata IP-adresser i det lokala nätverket måste du eller din leverantör översätta dem till offentliga IP-adresser [med hjälp av NAT](expressroute-nat.md).
- **QoS**: Skype för företag har olika tjänster (t.ex. röst, video, text) som kräver särskild QoS-behandling. Du och din leverantör bör följa [QoS-kraven](expressroute-qos.md).
- **Nätverkssäkerhet**: Du bör fundera över [nätverkssäkerheten](../best-practices-network-security.md) när du ansluter till Microsoft Cloud via ExpressRoute.
 
## Office 365

Om du planerar att aktivera Office 365 på ExpressRoute, läser du följande dokument för att få mer information om kraven för Office 365.


- [Översikt över ExpressRoute för Office 365](https://support.office.com/en-us/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
- [Routning med ExpressRoute för Office 365](https://support.office.com/en-us/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
- [URL:er och IP-adressintervall för Office 365](https://support.office.com/en-us/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
- [Nätverksplanering och prestandajustering för Office 365](https://support.office.com/en-us/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
- [Räknare och verktyg för nätverksbandbredd](https://support.office.com/en-us/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
- [Office 365-integration med lokala miljöer](https://support.office.com/en-us/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)

## CRM Online 
Om du planerar att aktivera CRM Online på ExpressRoute, läser du följande dokument för att få mer information om CRM Online

- [URL:er för CRM Online](https://support.microsoft.com/kb/2655102) och [IP-adressintervall](https://support.microsoft.com/kb/2728473)

## Nästa steg

- Mer information om ExpressRoute finns i [Vanliga frågor och svar om ExpressRoute](expressroute-faqs.md).
- Hitta en ExpressRoute-anslutningsleverantör. Se [ExpressRoute-partners och peeringplatser](expressroute-locations.md).
- Se kraven för [routning](expressroute-routing.md), [NAT](expressroute-nat.md) och [QoS](expressroute-qos.md).
- Konfigurera ExpressRoute-anslutningen.
    - [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md)
    - [Konfigurera routning](expressroute-howto-routing-classic.md)
    - [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-classic.md)




<!--HONumber=Oct16_HO3-->


