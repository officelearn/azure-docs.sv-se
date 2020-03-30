---
title: Säkerhetsfunktioner för hantering och övervakning – Microsoft Azure | Microsoft-dokument
description: Den här artikeln innehåller en översikt över de säkerhetsfunktioner och tjänster som Azure tillhandahåller för att underlätta hantering och övervakning av Azure-molntjänster och virtuella datorer.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5cf2827b-6cd3-434d-9100-d7411f7ed424
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 7ad7a29a92d25556190b4cf44f4e48158a6f0952
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73162741"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Översikt över azure-säkerhetshantering och övervakning
Den här artikeln innehåller en översikt över de säkerhetsfunktioner och tjänster som Azure tillhandahåller för att underlätta hantering och övervakning av Azure-molntjänster och virtuella datorer.

## <a name="role-based-access-control"></a>Rollbaserad Access Control

Rollbaserad åtkomstkontroll (RBAC) ger detaljerad åtkomsthantering för Azure-resurser. Genom att använda RBAC kan du bara ge personer den mängd åtkomst som de behöver för att utföra sina jobb. RBAC kan också hjälpa dig att se till att när människor lämnar organisationen, förlorar de åtkomst till resurser i molnet.

Läs mer:

* [Active Directory-teamblogg på RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Rollbaserad åtkomstkontroll i Azure](../../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Antimalware

Med Azure kan du använda programvara mot skadlig kod från större säkerhetsleverantörer som Microsoft, Symantec, Trend Micro, McAfee och Kaspersky. Den här programvaran hjälper till att skydda dina virtuella datorer från skadliga filer, adware och andra hot.

Microsoft Antimalware för Azure Cloud Services och Virtuella datorer ger dig möjlighet att installera en antimalware-agent för både PaaS-roller och virtuella datorer. Baserat på System Center Endpoint Protection ger den här funktionen beprövad lokal säkerhetsteknik till molnet.

Vi erbjuder även djup integration för Trends [Deep Security-](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) och [SecureCloud-produkter](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) i Azure-plattformen. Deep Security är en antiviruslösning och SecureCloud är en krypteringslösning. Deep Security distribueras inuti virtuella datorer via en tilläggsmodell. Genom att använda Azure Portal UI och PowerShell kan du välja att använda Djup säkerhet i nya virtuella datorer som snurras upp eller befintliga virtuella datorer som redan har distribuerats.

Symantec Endpoint Protection (SEP) stöds också på Azure. Genom portalintegration kan du ange att du tänker använda SEP på en virtuell dator. SEP kan installeras på en ny virtuell dator via Azure-portalen, eller så kan den installeras på en befintlig virtuell dator via PowerShell.

Läs mer:

* [Distribuera lösningar för skydd mot skadlig kod i Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware för Azure Cloud Services och virtuella datorer](antimalware.md)
* [Så här installerar och konfigurerar du Trend Micro Deep Security som en tjänst på en Virtuell Windows-dator](/azure/virtual-machines/windows/classic/install-trend)
* [Installera och konfigurera Symantec-slutpunktsskydd på en virtuell dator med Windows](/azure/virtual-machines/windows/classic/install-symantec)
* [Nya antimalware-alternativ för att skydda virtuella Azure-datorer](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multifaktorautentisering

Azure Multi-Factor Authentication är en autentiseringsmetod som kräver användning av mer än en verifieringsmetod. Det lägger till ett kritiskt andra säkerhetslager i användarloggningar och transaktioner.

Multifaktorautentisering hjälper till att skydda åtkomsten till data och program samtidigt som användarnas efterfrågan på en enkel inloggningsprocess tillgodoses. Den ger stark autentisering via en rad verifieringsalternativ (telefonsamtal, sms eller meddelande eller verifieringskod för mobilapp) och OATH-token från tredje part.

Läs mer:

* [Multifaktorautentisering](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Vad är Azure Multi-Factor Authentication?](/azure/active-directory/authentication/multi-factor-authentication)
* [Så fungerar Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Du kan använda Azure ExpressRoute för att utöka dina lokala nätverk till Microsoft Cloud via en dedikerad privat anslutning som underlättas av en anslutningsleverantör. Med ExpressRoute kan du upprätta anslutningar till Microsofts molntjänster som Azure, Office 365 och CRM Online. Anslutningen kan vara från:

* Ett IP VPN-nätverk (any-to-any).
* Ett point-to-point Ethernet-nätverk.
* En virtuell korsanslutning via en anslutningsleverantör på en samlokaliseringsfunktion.

ExpressRoute-anslutningar går inte över det offentliga internet. De kan erbjuda mer tillförlitlighet, snabbare hastigheter, lägre fördröjningar och högre säkerhet än typiska anslutningar via Internet.

Läs mer:

* [Teknisk översikt för ExpressRoute](../../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Virtuella nätverksgatewayer

VPN-gateways, även kallade Azure-gateways för virtuella nätverk, används för att skicka nätverkstrafik mellan virtuella nätverk och lokala platser. De används också för att skicka trafik mellan flera virtuella nätverk inom Azure (nätverk till nätverk). VPN-gateways ger säker anslutning mellan lokala och lokala enheter mellan Azure och din infrastruktur.

Läs mer:

* [Om VPN-gateways](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Översikt över Azure-nätverkssäkerhet](network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Ibland användare måste utföra privilegierade åtgärder i Azure-resurser eller andra SaaS-program. Detta innebär ofta att organisationer ger dem permanent privilegierad åtkomst i Azure Active Directory (Azure AD).

Detta är en växande säkerhetsrisk för molnbaserade resurser eftersom organisationer inte kan övervaka vad dessa användare gör med sin privilegierade åtkomst. Om ett användarkonto med privilegierad åtkomst har komprometterats kan en överträdelse dessutom påverka en organisations övergripande molnsäkerhet. Azure AD Privileged Identity Management hjälper till att lösa den här risken genom att sänka exponeringstiden för privilegier och öka insynen i användningen.  

Privilegierad identitetshantering introducerar konceptet med en tillfällig administratör för en roll eller "just in time"-administratörsåtkomst. Den här typen av administratör är en användare som behöver slutföra en aktiveringsprocess för den tilldelade rollen. Aktiveringsprocessen ändrar tilldelningen av användaren till en roll i Azure AD från inaktiv till aktiv, för en angiven tidsperiod.

Läs mer:

* [Azure AD Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md)
* [Komma igång med Azure Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection ger en konsoliderad vy över misstänkta inloggningsaktiviteter och potentiella sårbarheter för att skydda ditt företag. Identity Protection identifierar misstänkta aktiviteter för användare och privilegierade (admin) identiteter, baserat på signaler som:

* Brute-force attacker.
* Läckta referenser.
* Inloggningar från okända platser och infekterade enheter.

Genom att tillhandahålla meddelanden och rekommenderad reparation hjälper Identity Protection till att minska riskerna i realtid. Den beräknar användarrisk allvarlighetsgrad. Du kan konfigurera riskbaserade principer för att automatiskt skydda programåtkomst från framtida hot.

Läs mer:

* [Identitetsskydd för Azure Active Directory](/azure/active-directory/active-directory-identityprotection)
* [Kanal 9: Azure AD och Identity Show: Förhandsversion av identitetsskydd](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Azure Security Center hjälper dig att förebygga, identifiera och reagera på hot. Security Center ger dig ökad insyn i och kontroll över säkerheten för dina Azure-resurser. Det ger integrerad säkerhetsövervakning och principhantering över dina Azure-prenumerationer. Det hjälper till att upptäcka hot som annars skulle gå obemärkt förbi och fungerar med ett brett ekosystem av säkerhetslösningar.

Security Center hjälper dig att optimera och övervaka säkerheten för dina Azure-resurser genom att:

* Så att du kan definiera principer för dina Azure-prenumerationsresurser enligt:
  * Ditt företags säkerhetsbehov.
  * Typ av program eller känslighet för data i varje prenumeration.
* Övervaka tillståndet för dina virtuella Azure-datorer, nätverk och program.
* Tillhandahålla en lista över prioriterade säkerhetsaviseringar, inklusive aviseringar från integrerade partnerlösningar. Det ger också den information som du behöver för att snabbt undersöka en attack och rekommendationer om hur du åtgärdar den.

Läs mer:

* [Introduktion till Azure Security Center](../../security-center/security-center-intro.md)
* [Förbättra dina säkra poäng i Azure Security Center](../../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Intelligent säkerhetsdiagram

Intelligent Security Graph ger hotskydd i realtid i Microsofts produkter och tjänster. Den använder avancerad analys som länkar en enorm mängd hot intelligens och säkerhetsdata för att ge insikter som kan stärka organisationssäkerheten. Microsoft använder avancerad analys – bearbetning av mer än 450 miljarder autentiseringar per månad, genomsökning av 400 miljarder e-postmeddelanden för skadlig programvara och nätfiske och uppdatering av en miljard enheter – för att ge rikare insikter. Dessa insikter kan hjälpa din organisation att upptäcka och reagera på attacker snabbt.

* [Intelligent säkerhetsdiagram](https://www.microsoft.com/security/intelligence)

## <a name="next-steps"></a>Efterföljande moment
Läs mer om modellen med [delat ansvar](shared-responsibility.md) och vilka säkerhetsuppgifter som hanteras av Microsoft och vilka uppgifter som hanteras av dig.

Mer information om säkerhetshantering finns [i Säkerhetshantering i Azure](management.md).
