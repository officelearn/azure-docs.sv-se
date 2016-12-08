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
ms.date: 11/28/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 0c946ce6a96f2e3644b9890dad5d60a35ad4bcb7
ms.openlocfilehash: 10184c52d532eb56e66212fafdea3d059b0c43e3


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

Partnerlösningar som redan har distribuerats i Security Center kan enkelt nås från panelen Partnerlösning på huvudinstrumentpanelen i Security Center:

![Partnerintegration](./media/security-center-partner-integration/security-center-partner-integration-fig1.png)

Följ dessa steg om du vill distribuera en ny partnerlösning baserat på en Security Center-rekommendation:

> [!NOTE]
> I stegen i följande exempel förutsätter vi att du har en arbetsbelastning som du vill skydda med en brandvägg för webbaserade program.

1. Klicka på panelen **Rekommendationer** på Security Center-instrumentpanelen.
2. Klicka på **Add web application firewall** (Lägg till brandvägg för webbaserade program) på bladet **Rekommendationer**.
3. Klicka på programnamnet under bladet **Add web application firewall** (Lägg till brandvägg för webbaserade program).
4. På bladet **Add web application firewall** (Lägg till brandvägg för webbaserade program) klickar du på **Skapa ny**.
5. Bladet **Create a new Web Application Firewall** (Skapa en ny brandvägg för webbaserade program) visar en lista över de partner som erbjuder brandväggsfunktioner för webbprogram.
6. Välj lämplig partnerlösning och följ anvisningarna (som kan variera beroende på partner).

Den övergripande distributionsupplevelsen i det här steget kan variera beroende på partner. Mer information om hur du hanterar partnerlösningar i Security Center finns i [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md).

## <a name="see-also"></a>Se även
Det här dokumentet beskriver hur du integrerar partnerlösningar i Azure Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md)
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Säkerhetsaviseringar per typ i Azure Security Center](security-center-alerts-type.md)
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar Azure-resursernas hälsa.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.



<!--HONumber=Nov16_HO5-->


