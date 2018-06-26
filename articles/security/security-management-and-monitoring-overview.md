---
title: Azure-säkerhetshantering och övervakning-översikt | Microsoft Docs
description: Den här artikeln innehåller en översikt över funktioner och tjänster som Azure tillhandahåller för att underlätta hantering och övervakning av Azure-molntjänster och virtuella datorer.
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
ms.openlocfilehash: 9e538ac39af5b6df44860a4a70b0fd1e058c060c
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752296"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Azure-säkerhetshantering och övervakning-översikt
Azure tillhandahåller säkerhetsmekanismer att underlätta hantering och övervakning av Azure-molntjänster och virtuella datorer (VM). Den här artikeln innehåller en översikt över dessa kärnfunktioner för säkerhet och tjänster. Länkar som artiklar som ger information om varje så kan du läsa mer.

Säkerheten för din Microsoft-molntjänster är en koppling och delat ansvar mellan dig och Microsoft. Microsoft ansvarar för Azure-plattformen och den fysiska säkerheten för sina datacenter (med hjälp av säkerhetsskydd som låst Aktivitetsikon-posten dörrar, avgränsningstecken och skydd). Azure tillhandahåller starkt säkerhetsnivåer moln i lagret för programvara som uppfyller behoven för säkerhet, sekretess och kompatibilitet för sina kunder.

Du äger dina data och identiteter, ansvar för att skydda dem säkerheten för dina lokala resurser och säkerhet för moln-komponenter som du har kontroll. Microsoft ger dig säkerhetskontroller och funktioner som hjälper dig att skydda dina data och program. Din graden av ansvar för säkerhet baserat på vilken typ av tjänst i molnet.

I följande tabell sammanfattas saldo ansvar mellan Microsoft och kunden.

![Delat ansvar][1]

Läs mer om säkerhetshantering [säkerhetshantering i Azure](azure-security-management.md).

## <a name="role-based-access-control"></a>Rollbaserad Access Control
Rollbaserad åtkomstkontroll (RBAC) ger detaljerad åtkomsthantering för Azure-resurser. Med RBAC kan ge du användare bara åtkomstnivå som de behöver för att utföra sitt arbete. RBAC kan du också se till att när en användare lämnar organisationen, de kan komma åt resurser i molnet.

Läs mer:

* [Active Directory-teamets blogg på RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Rollbaserad åtkomstkontroll i Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Programvara mot skadlig kod
Med Azure, kan du använda program mot skadlig kod från leverantörer av större säkerhet, till exempel Microsoft, Symantec, Trend Micro, McAfee och Kaspersky. Den här programvaran skyddar dina virtuella datorer från skadliga filer, annonsprogram och andra hot.

Microsoft Antimalware för virtuella datorer och Azure Cloud Services ger dig möjlighet att installera en agent för program mot skadlig kod för virtuella datorer och PaaS-roller. Baserat på System Center Endpoint Protection ger den här funktionen beprövade lokalt säkerhetsteknik för molnet.

Vi erbjuder även djupgående integrering för Trenders [djup säkerhet](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) och [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) produkter i Azure-plattformen. Djupgående säkerhet är en antivirus och SecureCloud är en lösning för kryptering. Djupgående säkerhet distribueras i virtuella datorer via en modell för tillägget. Genom att använda Azure portal UI och PowerShell kan välja du att använda djup säkerhet i nya virtuella datorer som skapas eller befintliga virtuella datorer som redan har distribuerats.

Symantec Endpoint Protection (SEP) stöds även på Azure. Du kan ange som du tänker använda SEP på en virtuell dator via portalen integrering. SEP kan installeras på en ny virtuell dator via Azure portal eller så kan installeras på en befintlig virtuell dator via PowerShell.

Läs mer:

* [Distribuera lösningar för skydd mot skadlig kod i Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft program mot skadlig kod för Azure-molntjänster och virtuella datorer](azure-security-antimalware.md)
* [Hur du installerar och konfigurerar Trend Micro djup Security som en tjänst på en Windows VM](../virtual-machines/windows/classic/install-trend.md)
* [Hur du installerar och konfigurerar Symantec Endpoint Protection på en Windows VM](../virtual-machines/windows/classic/install-symantec.md)
* [Nya alternativ för program mot skadlig kod för att skydda virtuella Azure-datorer](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication
Azure Multi-Factor Authentication är en autentiseringsmetod som kräver att mer än en verifieringsmetod. Läggs ett kritiskt andra säkerhetslager till användarinloggningar och transaktioner. 

Multi-Factor Authentication hjälper dig att skydda åtkomst till data och program och uppfyller efterfrågan från användarna för en process för enkel inloggning. Den ger stark autentisering via ett intervall av alternativ för verifiering (telefonsamtal, textmeddelande eller mobilapp meddelande eller verifiering kod) och OATH-token från tredje part.

Läs mer:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Vad är Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Hur Azure Multi-Factor Authentication fungerar](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute
Du kan använda Azure ExpressRoute utökar ditt lokala nätverk till Microsoft Cloud via en dedikerad privata anslutning möjliggörs med hjälp av en provider för anslutningen. Du kan upprätta anslutningar till Microsoft-molntjänster som Azure, Office 365 och CRM Online med ExpressRoute. Anslutningen kan vara från:

- Ett valfritt-till-alla (IP VPN)-nätverk.
- Ett point-to-point Ethernet-nätverk.
- En virtuell cross-anslutning via en anslutning-providern på en anläggning för samplacering. 

ExpressRoute-anslutningar går inte via det offentliga internet. Erbjuder flera tillförlitlighet, högre hastighet, lägre latens och högre säkerhet än vanliga anslutningar över internet.

Läs mer:

* [Teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Virtuella nätverksgatewayer
VPN-gatewayer, även kallat Azure virtuella nätverks-gateway, för att skicka trafik mellan virtuella nätverk och lokala platser. De används också för att skicka trafik mellan flera virtuella nätverk i Azure (nätverk till nätverk). VPN-gatewayer ger säker anslutning mellan Azure och din infrastruktur.

Läs mer:

* [Om VPN-gatewayer](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Översikt över säkerheten i Azure-nätverk](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management
Ibland behöver användare att utföra Privilegierade åtgärder i Azure-resurser eller andra SaaS-program. Detta innebär ofta organisationer ge dem permanent privilegierad åtkomst i Azure Active Directory (AD Azure). 

Detta är en allt större säkerhetsrisk för molnbaserade resurser eftersom organisationer inte tillräckligt övervaka vad användarna gör med deras privilegierad åtkomst. Om ett användarkonto med privilegierad åtkomst äventyras, kan dessutom som en överträdelse påverkar organisationens övergripande säkerheten för molnet. Azure AD Privileged Identity Management hjälper till att lösa den här risken genom att minska exponeringstid privilegier och öka insyn i användning.  

Privileged Identity Management introducerar konceptet för en temporär administratör för en roll eller just-in-time ”administratörsåtkomst. Den här typen av administratör är en användare behöver för att slutföra aktiveringsprocessen för som tilldelats rollen. Aktiveringen ändras tilldelningen av användaren till en roll i Azure AD från inaktiva till aktiv under en viss tid period.

Läs mer:

* [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
* [Kom igång med Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## <a name="identity-protection"></a>Identity Protection
Azure AD Identity Protection ger en samlad vy över inloggning aktiviteter och potentiella sårbarheter för att skydda din verksamhet. Identity Protection upptäcker misstänkta aktiviteter för användare och Privilegierade (admin) identiteter, baserat på signaler som:

- Brute force-attacker.
- Läckta autentiseringsuppgifter.
- Inloggningar från okända platser och infekterade enheter.

Genom att tillhandahålla meddelanden och rekommenderade åtgärder, hjälper Identity Protection till att minska riskerna i realtid. Användaren risk allvarlighetsgrad beräknas. Du kan konfigurera risk-baserade principer för att skydda programmet åtkomst till från framtida problem automatiskt.

Läs mer:

* [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
* [Channel 9: Azure AD och Identity visa: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center
Azure Security Center hjälper dig att förebygga, upptäcka och åtgärda hot. Security Center får du ökad insyn i och kontroll över säkerheten för dina Azure-resurser. Det ger integrerad säkerhet övervaka och hantera principer för dina Azure-prenumerationer. Det hjälper dig att identifiera hot som annars kanske skulle förbli oupptäckta och fungerar med ett vittomfattande ekosystem med säkerhetslösningar.

Security Center hjälper dig att optimera och övervaka säkerheten för dina Azure-resurser genom att:

* Gör att du kan definiera principer för resurserna i Azure-prenumeration enligt:
  * Företagets säkerhet måste.
  * Typ av program eller datakänslighetsnivå i varje prenumeration.
* Övervaka status för din virtuella Azure-datorer, nätverk och program.
* Att tillhandahålla en lista med prioritetssorterade säkerhetsaviseringar, inklusive aviseringar från integrerade partnerlösningar. Det ger också information som du behöver undersöka en attack och rekommendationer om hur du åtgärdas snabbt.

Läs mer:

* [Introduktion till Azure Security Center](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png
