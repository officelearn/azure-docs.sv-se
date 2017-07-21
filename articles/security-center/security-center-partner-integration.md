---
title: Partnerintegration i Azure Security Center | Microsoft Docs
description: "Läs hur Azure Security Center integrerar med partners för att förbättra den övergripande säkerheten för dina Azure-resurser."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: f76de4efe3d4328a37f86f986287092c808ea537
ms.openlocfilehash: 4d0909e926de14a0cbe9799b969ac7a1946d69d1
ms.contentlocale: sv-se
ms.lasthandoff: 07/10/2017


---
# <a name="partner-integration-in-azure-security-center"></a>Partnerintegrering i Azure Security Center

I den här artikeln beskriver vi hur Azure Security Center kan integreras med partners för att hjälpa dig att förbättra den övergripande säkerheten. Security Center ger dig en integrerad upplevelse i Azure och drar nytta av Azure Marketplace för partnercertifiering och fakturering.

> [!NOTE] 
> Från och med juni 2017, använder sig Security Center av Microsoft Monitoring Agent för att samla in och lagra data. Mer information finns i [plattformsmigrering i Azure Security Center](security-center-platform-migration.md). Informationen i den här artikeln representerar Security Centers funktionalitet efter övergången till Microsoft Monitoring Agent.
>

## <a name="why-deploy-partner-solutions-from-security-center"></a>Varför ska du distribuera partnerlösningar från Security Center

De fyra viktigaste skälen att dra nytta av partnerintegrering i Security Center är:

- **Enkel distribution**. Det är mycket enklare att distribuera en partnerlösning genom att följa Security Center-rekommendationen. Distributionsprocessen kan automatiseras helt med hjälp av en standardiserad installation och nätverkstopologi. Kunder kan också välja ett halvautomatiserat alternativ för mer flexibilitet och anpassning.
- **Integrerade identifieringar**. Säkerhetshändelser från partnerlösningar samlas in, aggregeras och visas automatiskt som en del av aviseringarna och incidenterna i Security Center. Dessa händelser kombineras också med identifieringar från andra källor för att tillhandahålla funktioner för avancerad hotidentifiering.
- **Enhetlig hälsoövervakning och hantering**. Kunder kan använda integrerade hälsohändelser för att få en snabbövervakning av alla partnerlösningar. Grundläggande hantering finns tillgängligt med enkel åtkomst till avancerad konfiguration med hjälp av partnerlösningen.
- **Exportera till SIEM**. Kunder kan exportera alla Security Center- och partneraviseringar i Common Event Format (CEF) till lokala säkerhetsinformations och händelsehanterings-system (SIEM) med hjälp av Azure loggintegrering (förhandsversion).


## <a name="partners-that-integrate-with-security-center"></a>Partners som integreras med Security Center

För närvarande integrerar Security Center med följande lösning:

- Slutpunktsskydd ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec och [Microsoft Antimalware för Azure Cloud Services och Virtual Machines](https://docs.microsoft.com/azure/security/azure-security-antimalware)) 
- Brandvägg för webbaserade program ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) och [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/)) 
- Nästa generations brandvägg ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) och [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html)) 
- Sårbarhetsbedömning ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Framöver kommer Security Center att utöka antalet partners inom de här kategorierna samt lägga till fler kategorier. 

## <a name="deploy-a-partner-solution"></a>Distribuera en partnerlösning

Beroende på konfigurationen av din Azure-miljö och den säkerhetsprincip som du har definierat, kan Security Center rekommendera du distribuerar en partnerlösning. Rekommendationen vägleder dig genom processen att välja och installera en partnerlösning. Den övergripande distributionsupplevelsen kan variera beroende på typen av lösning och partner. Mer information finns i följande artiklar:

- [Installera slutpunktsskydd](security-center-install-endpoint-protection.md)
- [Lägga till en brandvägg för webbappar](security-center-add-web-application-firewall.md)
- [Lägg till en nästa generations brandvägg](security-center-add-next-generation-firewall.md)
- [Sårbarhetsbedömning inte installerad](security-center-vulnerability-assessment-recommendations.md)

## <a name="manage-partner-solutions"></a>Hantera partnerlösningar

Efter distributionen, kan du visa information om hälsotillståndet för lösningen och utföra grundläggande hanteringsåtgärder genom att gå in i **Security Center**-bladet och välja panelen **partnerlösningar**. Mer information om hur du hanterar partnerlösningar i Security Center finns i [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md).

![Partnerintegration](./media/security-center-partner-integration/security-center-partner-integration-fig1-1-newUI.png)

> [!NOTE]
> Symantec Endpoint Protection-stöd är begränsat till identifiering. Det finns inga hälsoaviseringar.
>

## <a name="see-also"></a>Se även

Den här artikeln berättade om hur man integrerar partnerlösningar i Azure Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Planerings- och bruksanvisningsguide för Security Center](security-center-planning-and-operations-guide.md)
* [Hantera och besvara säkerhetsaviseringar i Security Center](security-center-managing-and-responding-alerts.md)
* [Säkerhetsaviseringar efter typ i Security Center](security-center-alerts-type.md)
* [Övervakning av säkerhetshälsa i Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Övervaka partnerlösningar med Security Center](security-center-partner-solutions.md). Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Få svar på vanliga frågor om att använda tjänsten.
* [Azure säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

