---
title: Integrera säkerhetslösningar i Azure Security Center | Microsoft Docs
description: Läs hur Azure Security Center integrerar med partners för att förbättra den övergripande säkerheten för dina Azure-resurser.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/20/2018
ms.author: terrylan
ms.openlocfilehash: 1abf9efb5c0bed205ce5b87b1f055c14a11ce9ec
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245026"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrera säkerhetslösningar i Azure Security Center
Det här dokumentet hjälper dig att hantera säkerhetslösningar som redan är anslutna till Azure Security Center och lägga till nya.

## <a name="integrated-azure-security-solutions"></a>Integrerade Azure-säkerhetslösningar
Med Security Center är det enkelt att aktivera integrerade säkerhetslösningar i Azure. Fördelarna innefattar:

- **Förenklad distribution**: Security Center erbjuder effektiviserad etablering av integrerade partnerlösningar. Security Center kan etablera den nödvändiga agenten på dina virtuella datorer för lösningar som program mot skadlig kod och utvärdering av säkerhetsrisker. Security Center kan även hantera många nätverkskonfigurationer som krävs för brandväggsinstallationer.
- **Integrerade identifieringar**: Säkerhetshändelser från partnerlösningar samlas in, aggregeras och visas automatiskt som en del av aviseringarna och incidenterna i Security Center. Dessa händelser kombineras också med identifieringar från andra källor för att tillhandahålla funktioner för avancerad hotidentifiering.
- **Enhetlig övervakning och hantering av hälsa**: Kunderna kan använda integrerade hälsohändelser för att snabbt övervaka alla partnerlösningar. Grundläggande hantering finns tillgängligt med enkel åtkomst till avancerad konfiguration med hjälp av partnerlösningen.

Integrerade säkerhetslösningar omfattar för närvarande följande:

- Slutpunktsskydd ([Trend Micro](https://help.deepsecurity.trendmicro.com/azure-marketplace-getting-started-with-deep-security.html), [Symantec](https://www.symantec.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security) och [System Center Endpoint Protection](https://docs.microsoft.com/sccm/protect/deploy-use/endpoint-protection))
- Brandvägg för webbaserade program ([Barracuda](https://www.barracuda.com/products/webapplicationfirewall), [F5](https://support.f5.com/kb/en-us/products/big-ip_asm/manuals/product/bigip-ve-web-application-firewall-microsoft-azure-12-0-0.html), [Imperva](https://www.imperva.com/Products/WebApplicationFirewall-WAF), [Fortinet](https://www.fortinet.com/products.html) och [Azure Application Gateway](https://azure.microsoft.com/blog/azure-web-application-firewall-waf-generally-available/))
- Nästa generations brandvägg ([Check Point](https://www.checkpoint.com/products/vsec-microsoft-azure/), [Barracuda](https://campus.barracuda.com/product/nextgenfirewallf/article/NGF/AzureDeployment/), [Fortinet](http://docs.fortinet.com/d/fortigate-fortios-handbook-the-complete-guide-to-fortios-5.2) och [Cisco](http://www.cisco.com/c/en/us/td/docs/security/firepower/quick_start/azure/ftdv-azure-qsg.html) och [Palo Alto Networks](https://www.paloaltonetworks.com/products))
- Sårbarhetsbedömning ([Qualys](https://www.qualys.com/public-clouds/microsoft-azure/) och [Rapid7](https://www.rapid7.com/products/insightvm/))

> [!NOTE]
> Security Center installerar inte Microsoft Monitoring Agent på virtuella partnerenheter eftersom de flesta säkerhetsleverantörer inte tillåter att externa agenter körs på deras enhet.
>
>


| Slutpunktsskydd               | Plattformar                             | Installation av Security Center | Security Center Discovery |
|-----------------------------------|---------------------------------------|------------------------------|---------------------------|
| Windows Defender (Microsoft-programvara mot skadlig kod)                  | Windows Server 2016                   | Nej, inbyggd i OS           | Ja                       |
| System Center Endpoint Protection (Microsoft-programvara mot skadlig kod) | Windows Server 2012 R2, 2012, 2008 R2 | Via tillägg                | Ja                       |
| Trend Micro – Alla versioner         | Windows Server-familjen                 | Nej                           | Ja                       |
| Symantec v12.1.1100+              | Windows Server-familjen                 | Nej                           | Ja                       |
| McAfee v10 +                       | Windows Server-familjen                 | Nej                           | Ja                       |
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

1. Logga in på [Azure Portal](https://azure.microsoft.com/features/azure-portal/).

2. På menyn **Microsoft Azure** väljer du **Security Center**. **Security Center – Översikt** öppnas.

3. På menyn i Security Center väljer du **Säkerhetslösningar**.

  ![Security Center – Översikt](./media/security-center-partner-integration/overview.png)

Under **Säkerhetslösningar** kan du se information om hälsostatus för integrerade Azure-säkerhetslösningar och utföra grundläggande hanteringsåtgärder. Du kan även ansluta andra typer av datakällor för säkerhet, t.ex. Azure Active Directory Identity Protection-aviseringar och brandväggsloggar i Common Event Format (CEF).

### <a name="connected-solutions"></a>Anslutna lösningar

Avsnittet **Anslutna lösningar** innehåller säkerhetslösningar som för närvarande är anslutna till Security Center samt information om hälsostatusen för varje lösning.  

![Anslutna lösningar](./media/security-center-partner-integration/security-center-partner-integration-fig4.png)

Mer information finns i [Hantera anslutna partnerlösningar](security-center-partner-solutions.md).

### <a name="discovered-solutions"></a>Identifierade lösningar

Security Center identifierar automatiskt säkerhetslösningar som körs i Azure men som inte är anslutna till Security Center. Lösningarna visas i avsnittet **Identifierade lösningar**. Detta omfattar Azure-lösningar som [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection) samt partnerlösningar.

> [!NOTE]
> Standardnivån av Security Center krävs på prenumerationsnivån för funktionen identifierade lösningar. Mer information om prisalternativen för Security finns i [Priser](security-center-pricing.md).
>
>

Välj **ANSLUT** under en lösning om du vill integrera den med Security Center och få säkerhetsaviseringar.

![Identifierade lösningar](./media/security-center-partner-integration/security-center-partner-integration-fig5.png)

Security Center identifierar också lösningar som distribueras i prenumerationen och som kan vidarebefordra CEF-loggar (Common Event Format). Läs om hur du [ansluter en säkerhetslösning](quick-security-solutions.md) som använder CEF-loggar till Security Center.

### <a name="add-data-sources"></a>Lägg till datakällor

Avsnittet **Lägg till datakällor** innehåller andra tillgängliga datakällor som kan anslutas. Om du vill få anvisningar om att lägga till data från någon av dessa källor klickar du på **LÄGG TILL**.

![Datakällor](./media/security-center-partner-integration/security-center-partner-integration-fig7.png)


## <a name="next-steps"></a>Nästa steg

Den här artikeln berättade om hur man integrerar partnerlösningar i Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Ansluta Microsoft Advanced Threat Analytics till Azure Security Center](security-center-ata-integration.md)
* [Ansluta Azure Active Directory Identity Protection till Azure Security Center](security-center-aadip-integration.md)
* [Övervakning av säkerhetshälsa i Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Övervaka partnerlösningar med Security Center](security-center-partner-solutions.md). Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Få svar på vanliga frågor om användningen av Security Center.
* [Azure säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
