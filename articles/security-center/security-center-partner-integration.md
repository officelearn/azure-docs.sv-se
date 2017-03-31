---
title: Partnerintegration i Azure Security Center | Microsoft Docs
description: "Det här dokumentet beskriver hur Azure Security Center integrerar med partner för att förbättra den övergripande säkerheten för dina Azure-resurser."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 5001cd47b6ee51967d1286414ccefedd8e7e7813
ms.openlocfilehash: 095b5c9d1a888a4061450234f80c52c5834fbf53
ms.lasthandoff: 12/02/2016


---
# <a name="partner-integration-in-azure-security-center"></a>Partnerintegration i Azure Security Center
Det här dokumentet beskriver hur Azure Security Center integrerar med partner för att förbättra den övergripande säkerheten och skapa en integrerad upplevelse i Azure. Tjänsten utnyttjar dessutom Azure Marketplace för partnercertifiering och fakturering.

## <a name="why-deploy-partners-solutions-from-security-center"></a>Varför ska du distribuera partnerlösningar från Security Center?

De fyra viktigaste skälen till att använda partnerintegrationen i Security Center är:

- **Enkel distribution**: Det är mycket enklare att distribuera en partnerlösning genom att följa Security Center-rekommendationen. Distributionsprocessen kan automatiseras helt och hållet med en standardkonfiguration och standardnätverkstopologi. Alternativt kan kunder välja en delvis automatiserad metod som ger större flexibilitet och anpassning av konfigurationen.
- **Integrerade identifieringar**: Säkerhetshändelser från partnerlösningar samlas in, aggregeras och visas automatiskt som en del av aviseringarna och incidenterna i Security Center. Dessa händelser kombineras också med identifieringar från andra källor för att tillhandahålla funktioner för avancerad hotidentifiering.
- **Förenad hälsoövervakning och hälsohantering**: Med integrerade hälsohändelser kan kunder snabbt övervaka alla partnerlösningar. Grundläggande hantering är tillgängligt med enkel åtkomst till avancerad konfiguration med hjälp av partnerlösningen.
- **Exportera till SIEM**: Nu kan kunder exportera alla Security Center- och partneraviseringar i CEF-format till lokala SIEM-system med hjälp av Microsoft Azure Log Integration (förhandsversion)


## <a name="what-partners-are-integrated-with-security-center"></a>Vilka partner är integrerade med Security Center?
För närvarande integrerar Security Center med följande partner:

- Endpoint Protection (Trend Micro), 
- Web Application Firewall (Barracuda, F5, Imperva och snart Microsoft WAF och Fortinet), 
- Next Generation Firewall (Check Point, Barracuda och snart Fortinet och Cisco). 
- Lösningar för sårbarhetsbedömning (Qualys – förhandsversion). 

Framöver kommer Security Center att utökas med fler partner inom dessa befintliga kategorier, och nya kategorier kommer att läggas till. 

## <a name="how-to-deploy-a-partner-solution"></a>Hur distribuerar du en partnerlösning?

Beroende på konfigurationen av din Azure-miljö och den säkerhetsprincip som du har definierat kan Security Center rekommendera att en partnerlösning distribueras. Rekommendationen vägleder dig genom processen och hjälper dig att välja och installera en partnerlösning. Den övergripande distributionsmiljön kan variera beroende på typen av lösning och partner. Mer information finns på länkarna nedan:

- [Lägga till en brandvägg för webbappar](security-center-add-web-application-firewall.md)
- [Lägga till en nästa generations brandvägg](security-center-add-next-generation-firewall.md)
- [Installera slutpunktsskydd](security-center-install-endpoint-protection.md)
- [Sårbarhetsbedömning inte installerad](security-center-vulnerability-assessment-recommendations.md)

## <a name="how-to-manage-partner-solutions"></a>Så här hanterar du partnerlösningar

När en partnerlösning har distribuerats kan du visa information om lösningens hälsotillstånd och utföra grundläggande hanteringsuppgifter från panelen Partnerlösning på huvudinstrumentpanelen för Security Center. Mer information om hur du hanterar partnerlösningar i Security Center finns i [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md).

![Partnerintegration](./media/security-center-partner-integration/security-center-partner-integration-fig1-new.png)


## <a name="see-also"></a>Se även
Det här dokumentet beskriver hur du integrerar partnerlösningar i Azure Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md)
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Säkerhetsaviseringar per typ i Azure Security Center](security-center-alerts-type.md)
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar Azure-resursernas hälsa.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

