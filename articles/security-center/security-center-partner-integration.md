---
title: "Integrera säkerhetslösningar i Azure Security Center | Microsoft Docs"
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
ms.date: 10/26/2017
ms.author: yurid
ms.openlocfilehash: 0c0029d2dea293e71c6e3daf74b85f0234bfdffd
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrera säkerhetslösningar i Azure Security Center
Det här dokumentet hjälper dig att hantera säkerhetslösningar som redan är anslutna till Azure Security Center och lägga till nya.

## <a name="integrated-azure-security-solutions"></a>Integrerade Azure-säkerhetslösningar
Med Security Center är det enkelt att aktivera integrerade säkerhetslösningar i Azure. Fördelarna innefattar:

- **Förenklad distribution**: Security Center erbjuder effektiviserad etablering av integrerade partnerlösningar. Security Center kan etablera den nödvändiga agenten på dina virtuella datorer för lösningar som program mot skadlig kod och utvärdering av säkerhetsrisker. Security Center kan även hantera många nätverkskonfigurationer som krävs för brandväggsinstallationer.
- **Integrerade identifieringar**: Säkerhetshändelser från partnerlösningar samlas in, aggregeras och visas automatiskt som en del av aviseringarna och incidenterna i Security Center. Dessa händelser kombineras också med identifieringar från andra källor för att tillhandahålla funktioner för avancerad hotidentifiering.
- **Enhetlig övervakning och hantering av hälsa**: Kunderna kan använda integrerade hälsohändelser för att snabbt övervaka alla partnerlösningar. Grundläggande hantering finns tillgängligt med enkel åtkomst till avancerad konfiguration med hjälp av partnerlösningen.

Integrerade säkerhetslösningar omfattar för närvarande följande:

- Slutpunktsskydd ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), Symantec, Windows Defender, och System Center Endpoint Protection (SCEP))
- Brandvägg för webbaserade program ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/resources.html?limit=10&search=&document-type=data-sheets) och [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- Nästa generations brandvägg ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) och [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html))
- Sårbarhetsbedömning ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/))  

Slutpunktsskyddsintegreringen kan variera beroende på lösning. Följande tabell innehåller mer information om varje lösning:

| Slutpunktsskydd               | Plattformar                             | Installation av Security Center | Security Center Discovery |
|-----------------------------------|---------------------------------------|------------------------------|---------------------------|
| Windows Defender (Microsoft-programvara mot skadlig kod)                  | Windows Server 2016                   | Nej, inbyggd i OS           | Ja                       |
| System Center Endpoint Protection (Microsoft-programvara mot skadlig kod) | Windows Server 2012 R2, 2012, 2008 R2 | Via tillägg                | Ja                       |
| Trend Micro – Alla versioner         | Windows Server-familjen                 | Via tillägg                | Ja                       |
| Symantec v12+                     | Windows Server-familjen                 | Nej                           | Ja                        |
| MacAfee                           | Windows Server-familjen                 | Nej                           | Nej                        |
| Kaspersky                         | Windows Server-familjen                 | Nej                           | Nej                        |
| Sophos                            | Windows Server-familjen                 | Nej                           | Nej                        |



## <a name="how-security-solutions-are-integrated"></a>Så här integreras säkerhetslösningar
Azure-säkerhetslösningar som distribueras från Security Center ansluts automatiskt. Du kan även ansluta andra datakällor för säkerhet, till exempel:

- Azure AD Identity Protection
- Datorer som körs lokalt eller i andra moln
- Säkerhetslösning som stöder Common Event Format (CEF)
- Microsoft Advanced Threat Analytics

![Integrerings av partnerlösningar](./media/security-center-partner-integration/security-center-partner-integration-fig8.png)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Hantera integrerade Azure-säkerhetslösningar och andra datakällor

Efter distributionen kan du visa information om hälsotillståndet för den integrerade Azure-säkerhetslösningen och utföra grundläggande hanteringsåtgärder. Du kan även ansluta andra typer av datakällor för säkerhet, t.ex. Azure Active Directory Identity Protection-aviseringar och brandväggsloggar i Common Event Format (CEF). Välj Säkerhetslösningar i instrumentpanelen i Security Center.

### <a name="connected-solutions"></a>Anslutna lösningar

Avsnittet **Anslutna lösningar** innehåller säkerhetslösningar som för närvarande är anslutna till Security Center samt information om hälsostatusen för varje lösning.  

![Anslutna lösningar](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

### <a name="discovered-solutions"></a>Identifierade lösningar

Avsnittet **Identifierade lösningar** visar alla lösningar som har lagts till via Azure. Det visar även alla lösningar som Security Center föreslår ska anslutas till det.

![Identifierade lösningar](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center upptäcker automatiskt andra säkerhetslösningar som körs i Azure. Detta omfattar Azure-lösningar som [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) samt partnerlösningar som körs i Azure. Om du vill integrera de här lösningarna med Security Center väljer du **ANSLUT**.

### <a name="add-data-sources"></a>Lägg till datakällor

Avsnittet **Lägg till datakällor** innehåller andra tillgängliga datakällor som kan anslutas. Om du vill få anvisningar om att lägga till data från någon av dessa källor klickar du på **LÄGG TILL**.

![Datakällor](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>Nästa steg

Den här artikeln berättade om hur man integrerar partnerlösningar i Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Planerings- och bruksanvisningsguide för Security Center](security-center-planning-and-operations-guide.md)
* [Ansluta Microsoft Advanced Threat Analytics till Azure Security Center](security-center-ata-integration.md)
* [Ansluta Azure Active Directory Identity Protection till Azure Security Center](security-center-aadip-integration.md)
* [Övervakning av säkerhetshälsa i Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Övervaka partnerlösningar med Security Center](security-center-partner-solutions.md). Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Få svar på vanliga frågor om användningen av Security Center.
* [Azure säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
