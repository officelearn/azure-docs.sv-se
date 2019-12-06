---
title: Vad är för hands version av Azure Firewall Manager?
description: Lär dig mer om Azure Firewall Manager-funktioner
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 12/06/2019
ms.author: victorh
ms.openlocfilehash: cf8e6ca3a532dea29a413b1afdfc684ac8f08f17
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74869569"
---
# <a name="what-is-azure-firewall-manager-preview"></a>Vad är för hands version av Azure Firewall Manager?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager Preview är en säkerhetshanteringstjänst som ger dig en central säkerhetsprincip och väghantering för molnbaserade säkerhetsperimeterar. Det fungerar med [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources), en Microsoft-hanterad resurs som gör att du enkelt kan skapa Hubbs-och eker-arkitekturer. När säkerhets-och routningsprinciperna är associerade med en sådan hubb kallas den för en *[säker virtuell hubb](secured-virtual-hub.md)* . 

![Firewall-Manager](media/overview/firewallmanagerv3.png)

## <a name="azure-firewall-manager-preview-features"></a>Förhands gransknings funktioner i Azure Firewall Manager

I för hands versionen av Azure Firewall Manager finns följande funktioner:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Central Azure Firewall-distribution och konfiguration

Du kan distribuera och konfigurera flera Azure Firewall-instanser centralt som sträcker sig över olika Azure-regioner och prenumerationer. 

### <a name="hierarchical-policies-global-and-local"></a>Hierarkiska principer (globala och lokala)

Du kan använda Azure Firewall Manager Preview för att centralt hantera Azure Firewall-principer över flera skyddade virtuella nav. Dina centrala IT-team kan skapa globala brand Väggs principer som upprätthåller organisationens bred brand Väggs policy över team. Lokalt skapade brand Väggs principer tillåter en DevOps-självbetjänings modell för bättre flexibilitet.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrerat med säkerhets-som-en-tjänst från tredje part för avancerad säkerhet

Förutom Azure-brandväggen kan du integrera SECaaS-providers (Security as a Service) från tredje part för att ge ytterligare nätverks skydd för ditt VNet-och filial Internet-anslutningar.

- Trafik filtrering för VNet till Internet (V2I)

   - Filtrera utgående virtuell nätverks trafik med din önskade säkerhets leverantör från tredje part.
   - Använd avancerad användar medveten Internet skydd för dina moln arbets belastningar som körs på Azure.

- Trafik filtrering för gren till Internet (B2I)

   Dra nytta av din Azure-anslutning och global distribution för att enkelt lägga till tredjeparts filtrering för förgreningar i Internet scenarier.

Mer information om betrodda säkerhetsproviders finns i [Vad är betrodda säkerhets partner för Azure Firewall Manager (för hands version)?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Centraliserad flödes hantering

Dirigera snabbt trafik till din säkra hubb för filtrering och loggning utan att behöva konfigurera användardefinierade vägar manuellt (UDR) i ekrar virtuella nätverk. Du kan använda tredjepartsleverantörer för B2I-trafik (Branch to Internet), sida vid sida med Azure-brandväggen för gren till VNet (B2V), VNet till VNet (V2V) och VNet till Internet (V2I). Du kan också använda tredjepartsleverantörer för V2I-trafik filtrering så länge det inte krävs någon Azure-brandvägg för B2V eller V2V. 

## <a name="region-availability"></a>Tillgänglighet för regioner

Följande regioner stöds för den offentliga för hands versionen:

- Västeuropa, Nord Europa, Frankrike, centrala, Frankrike, södra, Storbritannien, södra, Storbritannien, västra
- Östra Australien, Australien, centrala Australien 2, Australien, sydöstra
- Kanada, centrala
- Östra USA, västra USA, östra USA 2, södra centrala USA, västra USA 2, centrala USA, norra centrala USA, västra centrala USA

Det går bara att skapa Azure Firewall-principer i dessa regioner, men de kan användas i olika regioner. Du kan till exempel skapa en princip i USA, västra och använda den i USA, östra. 

## <a name="known-issues"></a>Kända problem

För hands versionen av Azure Firewall Manager har följande kända problem:

|Problem  |Beskrivning  |Åtgärd  |
|---------|---------|---------|
|Manuellt skapade centrala virtuella nätverk stöds inte|Azure Firewall Manager stöder för närvarande nätverk som skapats med virtuella nav. Det finns ännu inte stöd för att använda egna manuellt skapade hubb-VNet.|Nu ska du använda Azure Firewall Manager med nav-och eker-nätverk som skapats med virtuella hubbar.<br>Korrigering pågår.
|Begränsningar för filtrering av tredje part|V2I trafik filtrering med tredjeparts leverantörer stöds inte med Azure Firewall B2V och V2V.|Undersöker för närvarande.|
|Delning av trafik stöds inte för närvarande|Det finns för närvarande inte stöd för att dela upp Office 365 och Azure offentlig PaaS-trafik. Det innebär att om du väljer en tredjeparts-Provider för V2I eller B2I skickas även all Azures offentliga PaaS och Office 365-trafik via partner tjänsten.|Undersöker för närvarande trafik delning på hubben.
|En hubb per region|Du kan inte ha mer än en hubb per region|Skapa flera virtuella WAN-näti en region.|
|Bas principerna måste finnas i samma region som den lokala principen|Skapa alla lokala principer i samma region som bas principen. Du kan fortfarande använda en princip som har skapats i en region på ett skyddat nav från en annan region.|Undersöker för närvarande.|
|Kommunikation mellan olika hubbar fungerar inte med skyddad virtuell hubb|Skyddad virtuell hubb till skyddad virtuell nav-kommunikation stöds inte ännu.|Undersöker för närvarande.|
|Alla skyddade virtuella hubbar som delar samma virtuella WAN-nätverk måste finnas i samma resurs grupp.|Det här beteendet är justerat med virtuella WAN-hubbar idag.|Skapa flera virtuella WAN-administratörer för att kunna skapa säkra virtuella hubbar i olika resurs grupper.|

## <a name="next-steps"></a>Nästa steg

- Läs [Översikt över Azure Firewall Manager Preview Deployment](deployment-overview.md)
- Läs mer om [skyddade virtuella nav](secured-virtual-hub.md).