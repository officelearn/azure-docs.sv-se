---
title: Azure-säkerhetshantering och övervakning av översikt | Microsoft Docs
description: " Azure tillhandahåller säkerhetsmekanismer att underlätta hantering och övervakning av Azure-molntjänster och virtuella datorer.  Den här artikeln innehåller en översikt över dessa kärnfunktioner för säkerhet och tjänster. "
services: security
documentationcenter: na
author: TerryLanfear
manager: StevenPo
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: terrylan
ms.openlocfilehash: 66a3a31574778bb1238498b523d51dc0d32ac22f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="azure-security-management-and-monitoring-overview"></a>Azure-säkerhetshantering och övervakning av översikt
Azure tillhandahåller säkerhetsmekanismer att underlätta hantering och övervakning av Azure-molntjänster och virtuella datorer. Den här artikeln innehåller en översikt över dessa kärnfunktioner för säkerhet och tjänster. Länkar som artiklar som ger information om varje så kan du läsa mer.

Säkerheten för din Microsoft-molntjänster är en partnerskap och delat ansvar mellan dig och Microsoft. Delat ansvar innebär Microsoft ansvarar för Microsoft Azure och fysiska säkerheten för sina datacenter (med hjälp av säkerhetsskydd som låst Aktivitetsikon post dörrar, avgränsningstecken och skydd). Azure tillhandahåller dessutom starkt säkerhetsnivåer moln i lagret för programvara som uppfyller behoven för säkerhet, sekretess och kompatibilitet för kunderna krävande.

Du äger dina data och identiteter, ansvar för att skydda dem säkerheten för dina lokala resurser och säkerhet för moln-komponenter som du har kontroll. Microsoft ger säkerhetskontroller och funktioner som hjälper dig att skydda dina data och program. Din graden av ansvar för säkerhet baserat på vilken typ av tjänst i molnet.

I följande tabell sammanfattas balans mellan ansvar för både Microsoft och kunden.

![Delat ansvar][1]

En mer grundlig genomgång i säkerhetshantering finns [säkerhetshantering i Azure](azure-security-management.md).

Här följer core-funktioner som beskrivs i den här artikeln:

* Rollbaserad Access Control
* Programvara mot skadlig kod
* Multi-Factor Authentication
* ExpressRoute
* Virtuella nätverksgatewayer
* Privileged identity management
* Identitetsskydd
* Security Center

## <a name="role-based-access-control"></a>Rollbaserad Access Control
Rollbaserad åtkomstkontroll (RBAC) ger detaljerad åtkomsthantering för Azure-resurser. Med RBAC kan bevilja du personer bara åtkomstnivå som de behöver för att utföra sitt arbete.  RBAC kan du också se till att när en användare lämnar organisationen de förlorar åtkomsten till resurser i molnet.

Läs mer:

* [Active Directory-teamets blogg på RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
* [Rollbaserad åtkomstkontroll i Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Programvara mot skadlig kod
Med Azure, kan du använda program mot skadlig kod från leverantörer av större säkerhet, till exempel Microsoft, Symantec, Trend Micro, McAfee och Kaspersky för att skydda dina virtuella datorer från skadliga filer, annonsprogram och andra hot.

Microsoft Antimalware ger dig möjlighet att installera en agent för program mot skadlig kod för virtuella datorer och PaaS-roller. Baserat på System Center Endpoint Protection ger den här funktionen beprövade lokalt säkerhetsteknik för molnet.

Vi erbjuder även djupgående integrering för Trenders [djup säkerhet](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ och [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ produkter i Azure-plattformen. DeepSecurity är en Antivirus och SecureCloud är en lösning för kryptering. DeepSecurity distribueras i virtuella datorer med hjälp av en modell för tillägget. Använder portalens användargränssnitt och PowerShell kan välja du att använda DeepSecurity i nya virtuella datorer som skapas eller befintliga virtuella datorer som redan har distribuerats.

Skydd för Symantec-slutpunkt (SEP) stöds även på Azure. Kunder kan via portalen integration ange att de avser att använda SEP på en virtuell dator. SEP kan installeras på en helt ny virtuell dator via Azure portal eller kan installeras på en befintlig virtuell dator med hjälp av PowerShell.

Läs mer:

* [Distribuera lösningar för skydd mot skadlig kod i Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft program mot skadlig kod för Azure-molntjänster och virtuella datorer](azure-security-antimalware.md)
* [Hur du installerar och konfigurerar Trend Micro djup Security som en tjänst på en Windows VM](../virtual-machines/windows/classic/install-trend.md)
* [Hur du installerar och konfigurerar Symantec Endpoint Protection på en Windows VM](../virtual-machines/windows/classic/install-symantec.md)
* [Nya alternativ för program mot skadlig kod för att skydda virtuella Azure-datorer – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Azure Multi-Factor authentication (MFA) är en autentiseringsmetod som kräver att mer än en verifieringsmetod och lägger till ett kritiskt andra säkerhetslager till användarinloggningar och transaktioner. MFA hjälper till att skydda åtkomst till data och program och uppfyller efterfrågan från användarna för en process för enkel inloggning. Den ger stark autentisering via en mängd alternativ för verifiering – telefonsamtal, textmeddelande eller mobilapp meddelande eller verifiering kod och tredjeparts OATH-token.

Läs mer:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Vad är Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Hur Azure Multi-Factor Authentication fungerar](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute
Med Microsoft Azure ExpressRoute kan du utöka ditt lokala nätverk till Microsoft-molnet via en dedikerad privat anslutning från en anslutningsleverantör. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster, till exempel Microsoft Azure, Office 365 och CRM Online. Anslutningen kan vara från ett ”any-to-any”-nätverk (IP VPN), ett ”point-to-point”-nätverk med Ethernet eller en virtuell korsanslutning via en anslutningsleverantör på en samlokaliseringsanläggning. ExpressRoute-anslutningar går inte via offentligt Internet. Det innebär att ExpressRoute-anslutningar är tillförlitligare, snabbare, har kortare svarstider och högre säkerhet än vanliga anslutningar över Internet.

Läs mer:

* [Teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Virtuella nätverksgatewayer
VPN-gatewayer, även kallat Azure virtuella nätverks-gateway, för att skicka trafik mellan virtuella nätverk och lokala platser. De används också för att skicka trafik mellan flera virtuella nätverk i Azure (VNet-till-VNet).  VPN-gatewayer ger säker anslutning mellan Azure och din infrastruktur.

Läs mer:

* [Om VPN-gatewayer](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Översikt över säkerheten i Azure-nätverk](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Ibland behöver användare att utföra Privilegierade åtgärder i Azure-resurser eller andra SaaS-program. Detta innebär ofta organisationer behöver ge dem permanent privilegierad åtkomst i Azure Active Directory (AD Azure). Detta är en allt större säkerhetsrisk för molnbaserade resurser eftersom organisationer inte tillräckligt övervaka vad användarna gör med deras privilegierad åtkomst.
Om ett användarkonto med privilegierad åtkomst äventyras, kan dessutom som en överträdelse påverka din övergripande säkerheten för molnet. Azure AD Privileged Identity Management hjälper till att lösa den här risken genom att minska exponeringstid privilegier och öka insyn i användning.  

Privileged Identity Management introducerar konceptet för en temporär administratör för en roll eller just-in-time ”administratörsåtkomst, vilket är en användare behöver för att slutföra aktiveringsprocessen för som tilldelats rollen. Aktiveringen ändras tilldelningen av användaren till en roll i Azure AD från inaktiva till aktiv under en viss tid period, till exempel åtta timmar.

Läs mer:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Kom igång med Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Identity Protection
Azure Active Directory (AD) Identity Protection ger en samlad vy över inloggning aktiviteter och potentiella sårbarheter för att skydda din verksamhet. Identity Protection upptäcker misstänkta aktiviteter för användare och Privilegierade (admin) identiteter, baserat på signaler som brute force-attacker läcka ut autentiseringsuppgifter och inloggningar från okända platser och infekterade enheter.

Genom att tillhandahålla meddelanden och rekommenderade åtgärder, hjälper Identity Protection till att minska riskerna i realtid. Beräkning av användaren risk allvarlighetsgrad och du kan konfigurera risk-baserade principer för att skydda programmet åtkomst till från framtida problem automatiskt.

Läs mer:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: Azure AD och Identity visa: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center
Azure Security Center hjälper dig att förebygga, upptäcka och åtgärda hot och ger du ökad insyn i, och kontroll över säkerheten för dina Azure-resurser. Härifrån kan du övervaka och hantera principer för alla Azure-prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Security Center hjälper dig att optimera och övervaka säkerheten för dina Azure-resurser genom att:

* Gör att du kan definiera principer för dina Azure-prenumerationsresurser utifrån företagets säkerhetskrav och typ av program eller datakänslighetsnivå i varje prenumeration.
* Övervaka status för din virtuella Azure-datorer, nätverk och program.
* Att tillhandahålla en lista med prioritetssorterade säkerhetsaviseringar, inklusive aviseringar från integrerade partnerlösningar, tillsammans med den information du behöver för att snabbt kan undersöka problemen och rekommendationer om hur du kan avhjälpa angrepp.

Läs mer:

* [Introduktion till Azure Security Center](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
