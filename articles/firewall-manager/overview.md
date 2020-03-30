---
title: Vad är förhandsversionen av Azure Firewall Manager?
description: Lär dig mer om Azure Firewall Manager-funktioner
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 03/13/2020
ms.author: victorh
ms.openlocfilehash: 149782f627d586e927c828506a7d4f1b5437b987
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79366282"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Vad är förhandsversionen av Azure Firewall Manager?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Förhandsversionen av Azure Firewall Manager är en säkerhetshanteringstjänst som tillhandahåller central säkerhetsprincip och väghantering för molnbaserade säkerhets perimeterer. 

Brandväggshanteraren kan tillhandahålla säkerhetshantering för två nätverksarkitekturtyper:

- **säker virtuell hubb**

   En [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) är en Microsoft-hanterad resurs som gör att du enkelt kan skapa hubb- och ekrararkitekturer. När säkerhet och routningsprinciper är associerade med ett sådant nav kallas det för ett *[skyddat virtuellt nav](secured-virtual-hub.md)*. 
- **virtuellt nätverk för nav**

   Det här är ett virtuellt standardnätverk för Azure som du skapar och hanterar själv. När säkerhetsprinciper är associerade med ett sådant nav kallas det för ett *virtuellt navnätverk*. För närvarande stöds endast Azure Firewall Policy. Du kan peer spoke virtuella nätverk som innehåller din arbetsbelastning servrar och tjänster. Du kan också hantera brandväggar i fristående virtuella nätverk som inte är peered till någon eker.

En detaljerad jämförelse av *skyddade virtuella hubb-* och *hubbarkitekturer finns* i Vilka är [arkitekturalternativen för Azure Firewall Manager?](vhubs-and-vnets.md).

![brandväggshanterare](media/overview/firewallmanagerv5.png)

## <a name="azure-firewall-manager-preview-features"></a>Förhandsfunktioner i Azure Firewall Manager

Förhandsversionen av Azure Firewall Manager innehåller följande funktioner:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Central distribution och konfiguration av Azure-brandväggen

Du kan distribuera och konfigurera flera Azure-brandväggsinstanser som sträcker sig över olika Azure-regioner och prenumerationer centralt. 

### <a name="hierarchical-policies-global-and-local"></a>Hierarkiska principer (globala och lokala)

Du kan använda förhandsversionen av Azure Firewall Manager för att centralt hantera Azure-brandväggsprinciper över flera säkra virtuella hubbar. Dina centrala IT-team kan skapa globala brandväggsprinciper för att tillämpa en brandväggspolicy för hela organisationen i teamen. Lokalt författade brandväggsprinciper tillåter en DevOps självbetjäningsmodell för bättre flexibilitet.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrerad med säkerhet som en tjänst från tredje part för avancerad säkerhet

Förutom Azure-brandväggen kan du integrera TREDJEPARTSLEVERANTÖRER av säkerhet som en tjänst (SECaaS) för att tillhandahålla ytterligare nätverksskydd för ditt virtuella nätverk och filialanslutningar till Internet.

Den här funktionen är endast tillgänglig med säkra virtuella hubbdistributioner.

- VNet till Internet (V2I) trafikfiltrering

   - Filtrera utgående virtuell nätverkstrafik med din önskade säkerhetsleverantör från tredje part.
   - Utnyttja avancerat användarmedvetna Internetskydd för dina molnarbetsbelastningar som körs på Azure.

- Filtrering av trafikfiltrering för gren till Internet (B2I)

   Utnyttja din Azure-anslutning och globala distribution för att enkelt lägga till filtrering från tredje part för gren till Internet-scenarier.

Mer information om betrodda säkerhetsleverantörer finns i [Vad är betrodda säkerhetspartner för Azure Firewall Manager (förhandsversion)?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Centraliserad rutthantering

Dirigera enkelt trafik till din säkra hubb för filtrering och loggning utan att manuellt ställa in ANVÄNDARDEFINIERAde vägar (UDR) i ekriska virtuella nätverk. 

Den här funktionen är endast tillgänglig med säkra virtuella hubbdistributioner.

Du kan använda tredjepartsleverantörer för B2I-trafikfiltrering (Branch to Internet), sida vid sida med Azure Firewall for Branch to VNet (B2V), VNet to VNet (V2V) och VNet to Internet (V2I). Du kan också använda tredjepartsleverantörer för V2I-trafikfiltrering så länge Azure-brandväggen inte krävs för B2V eller V2V. 

## <a name="region-availability"></a>Regional tillgänglighet

Azure-brandväggsprinciper kan användas i olika regioner. Du kan till exempel skapa en princip i västra USA och använda den i östra USA. 

## <a name="known-issues"></a>Kända problem

Förhandsversionen av Azure Firewall Manager har följande kända problem:

|Problem  |Beskrivning  |Åtgärd  |
|---------|---------|---------|
|Begränsningar för filtrering från tredje part.|V2I-trafikfiltrering med tredjepartsleverantörer stöds inte med Azure Firewall B2V och V2V.|Undersöker|
|Trafikdelning stöds för närvarande inte.|Office 365 och Azure Public PaaS-trafikdelning stöds för närvarande inte. Att välja en tredjepartsleverantör för V2I eller B2I skickar därför också all Azure Public PaaS- och Office 365-trafik via partnertjänsten.|Undersöker trafikdelning vid navet.
|En säker virtuell hubb per region.|Du kan inte ha mer än ett säkert virtuellt nav per region.|Skapa flera virtuella WAN i en region.|
|Basprinciper måste vara i samma region som lokal politik.|Skapa alla lokala principer i samma region som basprincipen. Du kan fortfarande använda en princip som har skapats i en region på en skyddad hubb från en annan region.|Undersöker|
|Kommunikation mellan navet fungerar inte med Secured Virtual Hub|Säker virtual hubb till säker virtual hub-kommunikation stöds ännu inte.|Undersöker|
|Alla skyddade virtuella hubbar som delar samma virtuella WAN måste finnas i samma resursgrupp.|Detta är i linje med virtuella WAN-hubbar idag.|Skapa flera virtuella BUNTar så att säkra virtuella hubbar kan skapas i olika resursgrupper.|
|IP-grupper stöds inte i brandväggsprincipen.|IP-grupper är i offentlig förhandsversion och stöds för närvarande endast med traditionella brandväggsregler.|Fixa pågår.
|CSP-prenumerationer (Cloud Solution Provider) stöds inte.|För närvarande stöds inte [CSP-prenumerationer.](https://azure.microsoft.com/offers/ms-azr-0145p/)|Undersöker

## <a name="next-steps"></a>Nästa steg

- Granska [distributionsdistributionsöversikten för förhandsversionen av Azure Firewall Manager](deployment-overview.md)
- Läs mer om [säkra virtuella hubbar](secured-virtual-hub.md).