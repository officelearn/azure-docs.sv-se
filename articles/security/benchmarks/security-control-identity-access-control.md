---
title: Azure Security Control - Identitets- och åtkomstkontroll
description: Identitets- och åtkomstkontroll för Azure Security Control
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 473fe838f53df0a874a2c793792533e01b7c069a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408536"
---
# <a name="security-control-identity-and-access-control"></a>Säkerhetskontroll: Identitets- och åtkomstkontroll

Identitets- och åtkomsthanteringsrekommendationer fokuserar på att ta itu med problem som rör identitetsbaserad åtkomstkontroll, låsa ned administrativ åtkomst, aviseringar om identitetsrelaterade händelser, onormalt kontobeteende och rollbaserad åtkomstkontroll.

## <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Föra en inventering av administrativa räkenskaper

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 3.1 | 4.1 | Kund |

Azure AD har inbyggda roller som uttryckligen måste tilldelas och kan ifrågasättas. Använd Azure AD PowerShell-modulen för att utföra ad hoc-frågor för att identifiera konton som är medlemmar i administrativa grupper.

- [Skaffa en katalogroll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Så här skaffar du medlemmar i en katalogroll i Azure AD med PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

## <a name="32-change-default-passwords-where-applicable"></a>3.2: Ändra standardlösenord där så är tillämpligt

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 3,2 | 4.2 | Kund |

Azure AD har inte begreppet standardlösenord. Andra Azure-resurser som kräver ett lösenord tvingar ett lösenord att skapas med komplexitetskrav och en minsta lösenordslängd, som skiljer sig beroende på tjänsten. Du är ansvarig för program från tredje part och marknadsplatstjänster som kan använda standardlösenord.

## <a name="33-use-dedicated-administrative-accounts"></a>3.3: Använd särskilda administrativa konton

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 3.3 | 4.3 | Kund |

Skapa standardrutiner kring användningen av dedikerade administrativa konton. Använd Azure Security Center Identity and Access Management för att övervaka antalet administrativa konton.

Du kan också aktivera en Just-In-Time / Just-Enough-Access med hjälp av Azure AD Privileged Identity Management Privileged Roles för Microsoft Services och Azure Resource Manager. 

- [Läs mer om privilegierad identitetshantering](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/)

## <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Använd enkel inloggning (SSO) med Azure Active Directory

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 3.4 | 4.4 | Kund |

Använd azure active directory sso i azures i stället för att konfigurera enskilda fristående autentiseringsuppgifter per tjänst. Använd Azure Security Center Identity and Access Management-rekommendationer.

- [Förstå SSO med Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Använda multifaktorautentisering för all Azure Active Directory-baserad åtkomst

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 3.5 | 4.5, 11.5, 12.11, 16.3 | Kund |

Aktivera Azure AD MFA och följ Azure Security Center Identity and Access Management-rekommendationer.

- [Aktivera MFA i Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

- [Så här övervakar du identitet och åtkomst i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Använd dedikerade datorer (arbetsstationer för privilegierad åtkomst) för alla administrativa uppgifter

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 3.6 | 4.6, 11.6, 12.12 | Kund |

Använd PAWs (privilegierade åtkomstarbetsstationer) med MFA konfigurerat för att logga in på och konfigurera Azure-resurser.

- [Läs mer om arbetsstationer för privilegierad åtkomst](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

- [Aktivera MFA i Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

## <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: Logga och varna för misstänkta aktiviteter från administrativa konton

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 3.7 | 4.8, 4.9 | Kund |

Använd Azure Active Directory-säkerhetsrapporter för generering av loggar och aviseringar när misstänkt eller osäker aktivitet inträffar i miljön. Använd Azure Security Center för att övervaka identitets- och åtkomstaktivitet.

- [Identifiera Azure AD-användare som flaggats för riskfylld aktivitet](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [Så här övervakar du användarnas identitet och åtkomstaktivitet i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

## <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Hantera Azure-resurser från endast godkända platser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 3.8 | 11.7 | Kund |

Använd namngivna platser för villkorlig åtkomst för att tillåta åtkomst från endast specifika logiska grupperingar av IP-adressintervall eller länder/regioner.

- [Konfigurera namngivna platser i Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

## <a name="39-use-azure-active-directory"></a>3.9: Använd Azure Active Directory

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 3.9 | 16.1, 16.2, 16.4, 16.5, 16.6 | Kund |

Använd Azure Active Directory (AAD) som det centrala autentiserings- och auktoriseringssystemet. AAD skyddar data genom att använda stark kryptering för data i vila och under överföring. AAD saltar också, hashar och lagrar användaruppgifter på ett säkert sätt.

- [Så här skapar och konfigurerar du en AAD-instans](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

## <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelbundet granska och förena användaråtkomst

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 3.10 | 16.9, 16.10 | Kund |

Azure AD tillhandahåller loggar som hjälper dig att identifiera inaktuella konton. Dessutom kan du använda Azure Identity Access Reviews för att effektivt hantera gruppmedlemskap, åtkomst till företagsprogram och rolltilldelningar. Användaråtkomst kan granskas regelbundet för att se till att endast rätt användare har fortsatt åtkomst. 

- [Förstå Azure AD-rapportering](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

- [Så här använder du Azure Identity Access-granskningar](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

## <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: Övervaka försök att komma åt inaktiverade autentiseringsuppgifter

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 3.11 | 16.12 | Kund |

Du har tillgång till Azure AD-inloggningsaktivitet, granskning och riskhändelseloggkällor, vilket gör att du kan integrera med alla SIEM/Monitoring-verktyg.

Du kan effektivisera den här processen genom att skapa diagnostikinställningar för Azure Active Directory-användarkonton och skicka granskningsloggar och inloggningsloggar till en Logganalysarbetsyta. Du kan konfigurera önskade aviseringar i Log Analytics Workspace.

- [Så här integrerar du Azure Activity Logs i Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

## <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Varning på kontots avvikelse för inloggningsbeteende

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 3.12 | 16.13 | Kund |

Använd Azure AD Risk- och Identity Protection-funktioner för att konfigurera automatiska svar på identifierade misstänkta åtgärder relaterade till användaridentiteter. Du kan också ange data i Azure Sentinel för vidare undersökning.

- [Så här visar du riska 2-inloggningar för Azure AD](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Konfigurera och aktivera riskprinciper för identitetsskydd](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

- [Så här går du ombord på Azure Sentinel](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

## <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Ge Microsoft åtkomst till relevanta kunddata under supportscenarier

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 3.13 | 16 | Kund |

I supportscenarier där Microsoft behöver komma åt kunddata tillhandahåller Customer Lockbox ett gränssnitt där du kan granska och godkänna eller avvisa begäranden om kunddataåtkomst.

- [Förstå Customer Lockbox](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)


## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhetskontroll: [Dataskydd](security-control-data-protection.md)