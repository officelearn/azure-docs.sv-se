---
title: Azure Security benchmark v2 – identitets hantering
description: Identitets hantering för Azure Security benchmark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 09/20/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 2b21bea924ee8eaed37246ce55feaf8243e3e7d4
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408745"
---
# <a name="security-control-v2-identity-management"></a>Säkerhets kontroll v2: identitets hantering

Identitets hantering täcker kontroller för att upprätta en säker identitets-och åtkomst kontroll med hjälp av Azure Active Directory. Detta inkluderar användning av enkel inloggning, starka autentiseringar, hanterade identiteter (och tjänst principer) för program, villkorlig åtkomst och övervakning av konto avvikelser.

## <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: standardisera Azure Active Directory som system för central identitets-och autentisering

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| IM-1 | 16,1, 16,2, 16,4, 16,5 | IA-2, IA-8, AC-2, AC-3 |

Azure Active Directory (Azure AD) är Azures standard tjänst för identitets-och åtkomst hantering. Du bör standardisera Azure AD för att styra organisationens identitets-och åtkomst hantering i:
- Microsoft Cloud-resurser, till exempel Azure Portal, Azure Storage, Azure Virtual Machines (Linux och Windows), Azure Key Vault, PaaS och SaaS-program.

- Organisationens resurser, till exempel program i Azure eller företagets nätverks resurser.

Att skydda Azure AD bör vara en hög prioritet i din organisations säkerhets praxis för molnet. Azure AD ger en identitets säker Poäng för att hjälpa dig att utvärdera din position för identiteter i relation till Microsofts rekommendationer för bästa praxis. Använd poängen för att mäta hur nära konfigurationen matchar rekommendationerna för bästa praxis och för att göra förbättringar i din säkerhets position.

Obs! Azure AD har stöd för externa identitets leverantörer, vilket gör det möjligt för användare utan Microsoft-konto att logga in på sina program och resurser med sin externa identitet.

- [Innehav i Azure AD](../../active-directory/develop/single-and-multi-tenant-apps.md)

- [Så här skapar och konfigurerar du en Azure AD-instans](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

- [Definiera Azure AD-klienter](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)  

- [Använda externa identitets leverantörer för ett program](../../active-directory/external-identities/identity-providers.md)

- [Vad är en säker identitet i Azure AD](../../active-directory/fundamentals/identity-secure-score.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitets-och nyckel hantering](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys) 

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: hantera program identiteter säkert och automatiskt

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| IM-2 | Saknas | AC-2, AC-3, IA-2, IA-4, IA-9 |

För icke-mänskliga konton som tjänster eller automatisering använder du Azure Managed Identities, i stället för att skapa ett mer kraftfullt humant konto för att få åtkomst till resurser eller köra kod. Azure Managed identiteter kan autentiseras för Azure-tjänster och-resurser som stöder Azure AD-autentisering. Autentisering aktive ras via fördefinierade regler för åtkomst beviljande, Undvik hårdkodade autentiseringsuppgifter i källkod eller konfigurationsfiler. 

För tjänster som inte har stöd för hanterade identiteter använder du Azure AD för att skapa ett huvud namn för tjänsten med begränsade behörigheter på resurs nivå i stället.  Vi rekommenderar att du konfigurerar tjänstens huvud namn med autentiseringsuppgifter för certifikatet och att de ska återgå till klient hemligheter. I båda fallen kan Azure Key Vault användas tillsammans med Azure Managed identiteter, så att körnings miljön (till exempel en Azure-funktion) kan hämta autentiseringsuppgiften från nyckel valvet.

- [Hanterade Azure-identiteter](../../active-directory/managed-identities-azure-resources/overview.md)

- [Tjänster som stöder hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Huvudnamn för Azure-tjänsten](/powershell/azure/create-azure-service-principal-azureps)

- [Skapa ett huvud namn för tjänsten med certifikat](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)

Använd Azure Key Vault för säkerhets objekts registrering: autentisering # auktorisera-a-Security-huvud-till-åtkomst-nyckel-valv

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitets-och nyckel hantering](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Använd enkel inloggning för Azure AD (SSO) för program åtkomst

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| IM-3 | 4.4 | IA-2, IA-4 |

Azure AD ger identitets-och åtkomst hantering till Azure-resurser, moln program och lokala program. Identitets-och åtkomst hantering gäller företags identiteter som anställda, samt externa identiteter som partner, leverantörer och leverantörer.

Använd enkel inloggning för Azure AD för att hantera och skydda åtkomsten till organisationens data och resurser lokalt och i molnet. Anslut alla användare, program och enheter till Azure AD för sömlös, säker åtkomst och bättre synlighet och kontroll. 

- [Förstå Application SSO med Azure AD](../../active-directory/manage-apps/what-is-single-sign-on.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Identitets-och nyckel hantering](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Använd starka verifierings kontroller för alla Azure Active Directory-baserade åtkomst

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| IM-4 | 4,2, 4,4 4,5, 11,5, 12,11, 16,3 | AC-2, AC-3, IA-2, IA-4 |

Azure AD stöder starka verifierings kontroller med Multi-Factor Authentication (MFA) och starka metoder för lösen ords kryptering.  
- Multi-Factor Authentication: Aktivera Azure AD MFA och följ Azure Security Center rekommendationer för identitets-och åtkomst hantering för MFA-installationen. MFA kan tillämpas på alla användare, välja användare eller på nivån per användare baserat på inloggnings villkor och riskfaktorer. 

- Lösen ords kryptering: tre alternativ för lösen ords kryptering är tillgängliga: Windows Hello för företag, Microsoft Authenticator app och lokala autentiseringsmetoder som smartkort. 

För administratörer och privilegierade användare bör du se till att den högsta nivån för metoden stark autentisering används, följt av lämplig princip för stark autentisering för andra användare.

Om äldre lösenordsbaserad autentisering fortfarande används för Azure AD-autentisering, bör du tänka på att endast moln konton (användar konton som skapats direkt i Azure) har en standard lösen ords princip för bas linjen. Och hybrid konton (användar konton som kommer från lokala Active Directory) följer de lokala lösen ords principerna. När du använder lösenordsbaserad autentisering ger Azure AD en funktion för lösen ords skydd som hindrar användare från att ange lösen ord som är lätta att gissa. Microsoft tillhandahåller en global lista över blockerade lösen ord som uppdateras utifrån telemetri, och kunderna kan förstärka listan utifrån deras behov (t. ex. varumärkes-, kultur referenser osv.). Detta lösen ords skydd kan endast användas för moln-och hybrid konton. 

Obs! autentisering baserat på enbart autentiseringsuppgifter för lösen ord är känsligt för populära angrepps metoder. För högre säkerhet använder du stark autentisering, till exempel MFA och en princip för starka lösen ord. För program från tredje part och Marketplace-tjänster som kan ha standard lösen ord bör du ändra dem under installationen av den inledande tjänsten. 

- [Så här aktiverar du MFA i Azure](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Introduktion till alternativ för lösen ords lös autentisering för Azure Active Directory](../../active-directory/authentication/concept-authentication-passwordless.md)

- [Standard lösen ords princip för Azure AD](../../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts)

- [Eliminera Felaktiga lösen ord med Azure AD Password Protection](../../active-directory/authentication/concept-password-ban-bad.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Identitets-och nyckel hantering](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

## <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: övervaka och Varna vid konto avvikelser

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| SNABB MEDDELANDE – 5 | 4,8, 4,9, 16,12, 16,13 | AC-2, AC-3, AC-7, AU-6 |

Azure AD tillhandahåller följande data Källor: 
-   Inloggningar – inloggnings rapporten innehåller information om användningen av hanterade program och användar inloggnings aktiviteter.

-   Gransknings loggar – ger spårning genom loggar för alla ändringar som görs via olika funktioner i Azure AD. Exempel på gransknings loggar för loggade ändringar omfattar att lägga till eller ta bort användare, appar, grupper, roller och principer.

-   Riskfyllda inloggningar – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare.

-   Användare som har flaggats för risk – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats.

Dessa data källor kan integreras med Azure Monitor, Azure Sentinel eller SIEM system från tredje part.

Azure Security Center kan även Varna vid vissa misstänkta aktiviteter, till exempel ett högt antal misslyckade autentiseringsförsök och föråldrade konton i prenumerationen. 

Azure Advanced Threat Protection (ATP) är en säkerhetslösning som kan använda lokala Active Directory signaler för att identifiera, identifiera och undersöka avancerade hot, komprometterade identiteter och skadliga Insider-åtgärder.

- [Granska aktivitets rapporter i Azure AD](../../active-directory/reports-monitoring/concept-audit-logs.md)

- [Visa Azure AD-riskfyllda inloggningar](../../active-directory/identity-protection/overview-identity-protection.md)

- [Så här identifierar du Azure AD-användare som har flaggats för riskfylld aktivitet](../../active-directory/identity-protection/overview-identity-protection.md)

- [Övervaka användarnas identitets-och åtkomst aktiviteter i Azure Security Center](../../security-center/security-center-identity-access.md)

- [Aviseringar i Azure Security Center hot Intelligence Protection-modulen](../../security-center/alerts-reference.md)

- [Så här integrerar du Azures aktivitets loggar i Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Anslut data från Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md)

- [Azure Advanced Threat Protection](/azure-advanced-threat-protection/what-is-atp)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: begränsa åtkomsten till Azure-resursen baserat på villkor

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| IM-6 | Saknas | AC-2, AC-3 |

Använd villkorlig åtkomst i Azure AD för mer detaljerad åtkomst kontroll baserat på användardefinierade villkor, till exempel att kräva användar inloggningar från vissa IP-intervall för att använda MFA. En detaljerad hantering av autentiseringsbegäranden kan också användas via en princip för villkorlig åtkomst i Azure AD för olika användnings fall. 

- [Översikt över villkorlig Azure-åtkomst](../../active-directory/conditional-access/overview.md)

- [Vanliga principer för villkorlig åtkomst](../../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- [Konfigurera autentiseringsessionshantering med villkorsstyrd åtkomst](../../active-directory/conditional-access/howto-conditional-access-session-lifetime.md)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Identitets-och nyckel hantering](/azure/cloud-adoption-framework/organize/cloud-security-identity-keys)

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Hotinformation](/azure/cloud-adoption-framework/organize/cloud-security-threat-intelligence)

## <a name="im-7-eliminate-unintended-credential-exposure"></a>IM-7: eliminera oavsiktlig exponering för autentiseringsuppgifter

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| IM-7 | 18,1, 18,7 | IA-5 |

Implementera Azure DevOps Credential scanner för att identifiera autentiseringsuppgifter i koden. Med scannern för autentiseringsuppgifter kan du också flytta identifierade autentiseringsuppgifter till säkrare platser som Azure Key Vault.

För GitHub kan du använda intern hemlighet-genomsökning för att identifiera autentiseringsuppgifter eller annan form av hemligheter i koden.

- [Konfigurera inloggnings skannern](https://secdevtools.azurewebsites.net/helpcredscan.html)

- [GitHub Secret-genomsökning](https://docs.github.com/github/administering-a-repository/about-secret-scanning)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)

- [Statushantering](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="im-8-secure-user-access-to-legacy-applications"></a>IM-8: säker användar åtkomst till äldre program

| Azure-ID | CIS-kontroller v 7.1-ID: n | NIST SP800 – 53 R4-ID: n |
|--|--|--|--|
| IM-8 | 14,6 | AC-2, AC-3, SC-11 |

Se till att du har moderna åtkomst kontroller och övervakning av sessioner för äldre program och de data som de lagrar och bearbetar. Även om VPN används ofta för att komma åt äldre program, har de ofta bara grundläggande åtkomst kontroll och begränsad övervakning av sessionen.

Med Azure AD-programproxy kan du publicera äldre lokala program till fjärranslutna användare med enkel inloggning (SSO) samtidigt som du explicit verifierar tillförlitligheten för både fjärranslutna användare och enheter med villkorlig åtkomst i Azure AD. 

Alternativt är Microsoft Cloud App Security en CASB-tjänst (Cloud Access Security Broker) som kan tillhandahålla kontroller för att övervaka en användares programsessioner och blockera åtgärder (för både äldre lokala program och SaaS-program (Cloud program vara som en tjänst). 

- [Azure AD Programproxy](../../active-directory/manage-apps/application-proxy.md#what-is-application-proxy)

- [Metod tips för Microsoft Cloud App Security](/cloud-app-security/best-practices)

**Ansvar** : kund

**Kund säkerhets intressenter** ( [Läs mer](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Säkerhetsarkitektur](/azure/cloud-adoption-framework/organize/cloud-security-architecture) 

- [Infrastruktur- och slutpunktssäkerhet](/azure/cloud-adoption-framework/organize/cloud-security-architecture)

- [Programsäkerhet och DevSecOps](/azure/cloud-adoption-framework/organize/cloud-security-application-security-devsecops)