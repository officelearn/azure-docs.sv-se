---
title: Azure säkerhetshantering och övervakning-översikt | Microsoft Docs
description: Den här artikeln innehåller en översikt över säkerhetsfunktioner och tjänster som Azure tillhandahåller för att underlätta hantering och övervakning av Azure-molntjänster och virtuella datorer.
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
ms.date: 10/30/2018
ms.author: terrylan
ms.openlocfilehash: f79f94c277b02a9f377b90bf74763ac617f65c16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60597903"
---
# <a name="azure-security-management-and-monitoring-overview"></a>Azure säkerhetshantering och övervakning-översikt

Azure tillhandahåller säkerhetsmekanismer för enklare hantering och övervakning av Azure-molntjänster och virtuella datorer (VM). Den här artikeln innehåller en översikt över dessa grundläggande säkerhetsfunktionerna och tjänster. Länkar till artiklar som ger information om var och en så att du kan läsa mer.

Säkerheten för Microsoft cloud services är ett partnerskap och ett delat ansvar mellan dig och Microsoft. Microsoft ansvarar för Azure-plattformen och den fysiska säkerheten för sina datacenter (med hjälp av säkerhetsskydd som låst märket inmatning dörrar, avgränsningstecken och skydd). Azure ger stark säkerhetsnivåer molnet i lagret för programvara som uppfyller behoven för säkerhet, sekretess och efterlevnad för sina kunder.

Du äger dina data och identiteter, ansvaret för att skydda dem, säkerheten för dina lokala resurser och säkerheten i molnet komponenter som du har kontroll. Microsoft ger dig säkerhetskontroller och funktioner för att hjälpa dig att skydda dina data och program. Din graden av ansvar för säkerhet baseras på vilken typ av tjänst i molnet.

Följande diagram sammanfattar balans ansvar mellan Microsoft och kunden.

![Delat ansvar][1]

Läs mer om säkerhetshantering [säkerhetshantering i Azure](azure-security-management.md).

## <a name="role-based-access-control"></a>Rollbaserad Access Control

Rollbaserad åtkomstkontroll (RBAC) ger detaljerad åtkomsthantering för Azure-resurser. Med RBAC kan ge du användare endast mängden åtkomst som de behöver för att utföra sitt arbete. RBAC kan du också se till att när en användare lämnar organisationen, de förlorar åtkomst till resurser i molnet.

Läs mer:

* [Active Directory-teamets blogg på RBAC](https://cloudblogs.microsoft.com/enterprisemobility/?product=azure-active-directory)
* [Rollbaserad åtkomstkontroll i Azure](../role-based-access-control/role-assignments-portal.md)

## <a name="antimalware"></a>Programvara mot skadlig kod

Med Azure kan använda du program mot skadlig kod från stora säkerhetsleverantörer som Microsoft, Symantec, Trend Micro, McAfee och Kaspersky. Det här programmet kan du skydda dina virtuella datorer från skadliga filer, annonsprogram och andra hot.

Microsoft Antimalware för Azure Cloud Services och virtuella datorer ger dig möjlighet att installera en agent för program mot skadlig kod för virtuella datorer och PaaS-roller. Baserat på System Center Endpoint Protection kan den här funktionen ger dig beprövade lokala security-teknik till molnet.

Vi erbjuder också djupgående integrering för Trenders [Deep Security](https://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/) och [SecureCloud](https://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/) produkter i Azure-plattformen. Deep Security är en antivirus och SecureCloud är en lösning för kryptering. Deep Security distribueras i virtuella datorer via en modell för tillägget. Med hjälp av Azure-portalens användargränssnitt och PowerShell, kan du välja att använda Deep Security i nya virtuella datorer som är påskyndas eller befintliga virtuella datorer som redan har distribuerats.

Symantec Endpoint Protection (SEP) stöds även på Azure. Du kan ange att du tänker använda SEP på en virtuell dator via portalen integrering. SEP kan installeras på en ny virtuell dator via Azure portal eller den kan installeras på en befintlig virtuell dator via PowerShell.

Läs mer:

* [Distribuera lösningar för skydd mot skadlig kod i Azure Virtual Machines](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Microsoft Antimalware för Azure-molntjänster och virtuella datorer](azure-security-antimalware.md)
* [Hur du installerar och konfigurerar du Trend Micro Deep Security som en tjänst på en virtuell Windows-dator](../virtual-machines/windows/classic/install-trend.md)
* [Hur du installerar och konfigurerar du Symantec Endpoint Protection på en virtuell Windows-dator](../virtual-machines/windows/classic/install-symantec.md)
* [Nya alternativ för program mot skadlig kod för att skydda Azure-datorer](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure Multi-Factor Authentication är en autentiseringsmetod som kräver att fler än en verifieringsmetod. Den lägger till ett viktigt andra säkerhetslager till användarinloggningar och transaktioner. 

Multi-Factor Authentication hjälper till att skydda åtkomsten till data och program och tillgodoser samtidigt användarens önskemål för en enkel inloggningsprocess. Det ger stark autentisering via en rad alternativ för verifiering (telefonsamtal, SMS eller meddelande eller verifieringen mobilappkoden) och tredje parts OATH-token.

Läs mer:

* [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Vad är Azure Multi-Factor Authentication?](../active-directory/authentication/multi-factor-authentication.md)
* [Hur Azure Multi-Factor Authentication fungerar](../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="expressroute"></a>ExpressRoute

Du kan använda Azure ExpressRoute för att utöka ditt lokala nätverk till Microsoft Cloud via en dedikerad privat anslutning som är från en anslutningsleverantör. Med ExpressRoute kan upprätta du anslutningar till Microsofts molntjänster som Azure, Office 365 och CRM Online. Anslutningen kan vara från:

* Ett any-to-any (IP VPN)-nätverk.
* Ett mellan punkter med Ethernet-nätverk.
* En virtuell korsanslutning via en anslutningsleverantör i en samplaceringsmiljö. 

ExpressRoute-anslutningar går inte via offentliga internet. De kan erbjuda mer tillförlitlighet, snabbare hastigheter, kortare svarstider och högre säkerhet än vanliga anslutningar via internet.

Läs mer:

* [Teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md)

## <a name="virtual-network-gateways"></a>Virtuella nätverksgatewayer

VPN-gatewayer, även kallat Azures virtuella nätverksgatewayar används för att skicka nätverkstrafik mellan virtuella nätverk och lokala platser. De används också för att skicka trafik mellan flera virtuella nätverk i Azure (nätverk till nätverk). VPN-gatewayer ger säker korsanslutningar mellan Azure och din infrastruktur.

Läs mer:

* [Om VPN-gatewayer](../vpn-gateway/vpn-gateway-about-vpngateways.md)
* [Översikt över Azure network security](security-network-overview.md)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Ibland behöver användare att utföra Privilegierade åtgärder i Azure-resurser eller andra SaaS-program. Detta innebär ofta organisationer ge dem permanent privilegierad åtkomst i Azure Active Directory (AD Azure). 

Detta är en växande säkerhetsrisk för molnbaserade resurser eftersom organisationer inte kan tillräckligt övervaka vad användarna gör med sina privilegierad åtkomst. Om ett användarkonto med privilegierad åtkomst äventyras, kan dessutom att ett intrång påverkar organisationens övergripande molnsäkerhet. Azure AD Privileged Identity Management hjälper dig för att lösa den här risken genom att sänka exponeringstiden för privilegier och öka insyn i användning.  

Privileged Identity Management introduceras konceptet med en tillfällig administratör för en roll eller just-in-time ”administratörsåtkomst. Den här typen av administratör är en användare som behöver en aktiveringsprocess för den tilldelade rollen. Aktiveringen ändras tilldelningen av användaren till en roll i Azure AD från inaktiv till aktiv under en viss tid period.

Läs mer:

* [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md)
* [Kom igång med Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-getting-started.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection ger en samlad vy över misstänkta inloggningsaktiviteter och potentiella problem för att skydda din verksamhet. Identity Protection identifierar misstänkta aktiviteter för användare och Privilegierade (administratör)-identiteter, baserat på signaler som:

* Brute force-attacker.
* Läckta autentiseringsuppgifter.
* Inloggningar från okända platser och smittade enheter.

Genom att tillhandahålla meddelanden och rekommenderade åtgärder, hjälper Identity Protection till att minska riskerna i realtid. Riskens allvarlighetsgrad beräknas. Du kan konfigurera riskbaserade principer för att automatiskt skydda programmet komma åt mot framtida hot.

Läs mer:

* [Identitetsskydd för Azure Active Directory](../active-directory/active-directory-identityprotection.md)
* [Channel 9: Azure AD och Identity visa: Förhandsversionen av Identity Protection](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="security-center"></a>Security Center

Azure Security Center hjälper dig att förhindra, upptäcka och svara på hot. Security Center ger dig ökad insyn i och kontroll över säkerheten hos dina Azure-resurser. Det ger integrerad säkerhet övervaka och hantera principer för alla Azure-prenumerationer. Det hjälper dig att identifiera hot som kan annars oupptäckta och fungerar med ett vittomfattande ekosystem med säkerhetslösningar.

Security Center hjälper dig att optimera och övervaka säkerheten för dina Azure-resurser genom att:

* Gör att du kan definiera principer för dina Azure-prenumerationsresurser enligt:
  * Ditt företags säkerhetsbehov.
  * Typ av program eller efter Känslighetsnivån på datauppgifterna i respektive prenumeration.
* Övervaka tillståndet för din Azure-datorer, nätverk och program.
* Ger en lista över prioriterade säkerhetsvarningar, inklusive aviseringar från integrerade partnerlösningar. Den innehåller också information som du behöver för att snabbt undersöker ett angrepp och rekommendationer för hur du kan åtgärdas.

Läs mer:

* [Introduktion till Azure Security Center](../security-center/security-center-intro.md)
* [Förbättra dina säker poäng i Azure Security Center](../security-center/security-center-secure-score.md)

## <a name="intelligent-security-graph"></a>Intelligent Security Graph

Intelligent Security Graph ger skydd i realtid mot hot i Microsoftprodukter och tjänster. Det använder avancerad analys som länkar en omfattande mängd intelligens och säkerhet hotdata för att ge insikter som kan förstärka företagssäkerhet. Microsoft använder avancerad analys – bearbetar drygt 450 miljarder autentiseringar per månad, genomsöker 400 miljarder e-postmeddelanden efter skadlig programvara och nätfiske och uppdaterar en miljard enheter – att leverera bättre. Med hjälp av dessa insikter kan din organisation snabbt identifiera och förhindra angrepp.

* [Intelligent Security Graph](https://www.microsoft.com/security/intelligence)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png

