---
title: Metodtips för Azure AD B2C
titleSuffix: Azure AD B2C
description: Rekommendationer och metodtips att tänka på när du arbetar med Azure Active Directory B2C (Azure AD B2C).
services: active-directory-b2c
author: vigunase
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: vigunase
ms.subservice: B2C
ms.openlocfilehash: a76852a6e3cc5ffcdfcac62ce29fe47c97af3df1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136167"
---
# <a name="recommendations-and-best-practices-for-azure-active-directory-b2c"></a>Rekommendationer och metodtips för Azure Active Directory B2C

Följande metodtips och rekommendationer täcker några av de primära aspekterna av att integrera Azure Active Directory (Azure AD) B2C i befintliga eller nya programmiljöer.

## <a name="fundamentals"></a>Grunder

|  |  |
|--|--|
| Välj användarflöden för de flesta scenarier | Identity Experience Framework för Azure AD B2C är tjänstens kärnstyrka. Principer beskriver helt identitetsupplevelser som registrering, inloggning eller profilredigering. Azure AD B2C-portalen innehåller fördefinierade, konfigurerbara principer som kallas användarflöden för att hjälpa dig att ställa in de vanligaste identitetsuppgifterna. Med användarflöden kan du skapa fantastiska användarupplevelser på några minuter, med bara några klick. [Lär dig när du använder användarflöden jämfört med anpassade principer](custom-policy-overview.md#comparing-user-flows-and-custom-policies).|
| Appregistreringar | Alla program (webb, native) och API som skyddas måste registreras i Azure AD B2C. Om en app har både en webbversion och en inbyggd version av iOS och Android kan du registrera dem som ett program i Azure AD B2C med samma klient-ID. Läs om hur du [registrerar OIDC-, SAML-, webb- och inbyggda appar](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=applications). Läs mer om [programtyper som kan användas i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/application-types). |
| Flytta till månadsvis fakturering för aktiva användare | Azure AD B2C har flyttats från månatliga aktiva autentiseringar till månatliga active users (MAU) fakturering. De flesta kunder kommer att finna denna modell kostnadseffektiv. [Läs mer om fakturering för aktiva användare varje månad](https://azure.microsoft.com/updates/mau-billing/). |

## <a name="planning-and-design"></a>Planering och design

Definiera din program- och tjänstarkitektur, inventera aktuella system och planera migreringen till Azure AD B2C.

|  |  |
|--|--|
| Arkitekt en end-to-end-lösning | Inkludera alla dina programs beroenden när du planerar en Azure AD B2C-integrering. Tänk på alla tjänster och produkter som för närvarande finns i din miljö eller som kan behöva läggas till i lösningen, till exempel Azure Functions, CRM-system (Customer Relationship Management), Azure API Management gateway och lagringstjänster. Ta hänsyn till säkerhet och skalbarhet för alla tjänster. |
| Dokumentera användarnas upplevelser | Beskriv alla användarresor som dina kunder kan uppleva i ditt program. Inkludera alla skärmar och eventuella förgreningsflöden som de kan stöta på när de interagerar med identitets- och profilaspekterna i ditt program. Inkludera användbarhet, tillgänglighet och lokalisering i din planering. |
| Välj rätt autentiseringsprotokoll |  En uppdelning av de olika programscenarierna och deras rekommenderade autentiseringsflöden finns i [Scenarier och autentiseringsflöden som stöds](../active-directory/develop/authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows). |
| Pilot en proof-of-concept (POC) end-to-end användarupplevelse | Börja med våra [Microsoft-kodexempel](code-samples.md) och [communityexempel](https://github.com/azure-ad-b2c/samples). |
| Skapa en migreringsplan |Planering framåt kan göra migrering gå smidigare. Läs mer om [migrering av användare](user-migration.md).|
| Användbarhet kontra säkerhet | Din lösning måste hitta rätt balans mellan användbarhet för program och organisationens godtagbara risknivå. |
| Flytta lokala beroenden till molnet | För att säkerställa en flexibel lösning bör du överväga att flytta befintliga programberoenden till molnet. |
| Migrera befintliga appar till b2clogin.com | Utfasningen av login.microsoftonline.com trädde i kraft för alla Azure AD B2C-klienter den 4 december 2020. [Läs mer](b2clogin.md). |

## <a name="implementation"></a>Implementering

Under genomförandefasen bör du överväga följande rekommendationer.

|  |  |
|--|--|
| Redigera anpassade principer med Azure AD B2C-tillägget för Visual Studio-kod | Ladda ned Visual Studio-koden och det här community-byggda [tillägget från Visual Studio Code Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c). Även om inte en officiell Microsoft-produkt innehåller Azure AD B2C-tillägget för Visual Studio-kod flera funktioner som gör det enklare att arbeta med anpassade principer. |
| Lär dig hur du felsöker Azure AD B2C | Läs om hur du [felsöker anpassade principer](https://docs.microsoft.com/azure/active-directory-b2c/troubleshoot-custom-policies?tabs=applications) under utvecklingen. Lär dig hur ett normalt autentiseringsflöde ser ut och använd verktyg för att upptäcka avvikelser och fel. Använd till exempel [Application Insights](troubleshoot-with-application-insights.md) för att granska utdataloggar för användarresor. |
| Utnyttja vårt bibliotek med beprövade anpassade policymönster | Hitta [exempel på](https://github.com/azure-ad-b2c/samples) flera förbättrade användarresor för Azure AD B2C-kundidentitet och åtkomsthantering (CIAM). |


## <a name="testing"></a>Testning

Testa och automatisera din Azure AD B2C-implementering.

|  |  |
|--|--|
| Konto för global trafik | Använd trafikkällor från olika globala adresser för att testa prestanda- och lokaliseringskraven. Kontrollera att alla HTMLs, CSS och beroenden kan uppfylla dina prestandabehov. |
| Testning av funktionella gränssnitt och användargränssnitt | Testa användarens flöden från på nytt. Tillsätt syntetiska tester med några minuters mellanrum med Selen, VS Web Test, etc. |
| Penntestning | Innan du går live med din lösning, utför penetrationstester för att kontrollera att alla komponenter är säkra, inklusive eventuella beroenden från tredje part. Kontrollera att du har säkrat dina API:er med åtkomsttoken och använde rätt autentiseringsprotokoll för ditt programscenario. Läs mer om [penetrationstestning](https://docs.microsoft.com/azure/security/fundamentals/pen-testing) och [Microsoft Cloud Unified Penetration Testing Rules of Engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1). |
| A/B-testning | Flyg dina nya funktioner med en liten, slumpmässig uppsättning användare innan du rullar ut till hela din befolkning. Med JavaScript aktiverat i Azure AD B2C kan du integrera med A/B-testverktyg som Optimizely, Clarity och andra. |
| Belastningstestning | Azure AD B2C kan skalas, men ditt program kan skala endast om alla dess beroenden kan skalas. Ladda-testa dina API:er och CDN. |
| Begränsning |  Azure AD B2C begränsar trafiken om för många begäranden skickas från samma källa på kort tid. Använd flera trafikkällor när du `AADB2C90229` läser in testningen och hantera felkoden smidigt i dina program. |
| Automation | Använd kontinuerlig integrering och leverans (CI/CD) pipelines för att automatisera testning och distributioner, till exempel [Azure DevOps](deploy-custom-policies-devops.md). |

## <a name="operations"></a>Åtgärder

Hantera din Azure AD B2C-miljö.

|  |  |
|--|--|
| Skapa flera miljöer | För enklare drift och distributionsutbyggnad skapar du separata miljöer för utveckling, testning, förproduktion och produktion. Skapa Azure AD B2C-klienter för varje. |
| Använda versionskontrollen för dina anpassade principer | Överväg att använda GitHub, Azure Repos eller ett annat molnbaserat versionskontrollsystem för dina anpassade Azure AD B2C-principer. |
| Använda Microsoft Graph API för att automatisera hanteringen av dina B2C-klienter | Api:er för Microsoft Graph:<br/>Hantera [Identity Experience Framework](https://docs.microsoft.com/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta) (anpassade principer)<br/>[Nycklar](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)<br/>[Användarflöden](https://docs.microsoft.com/graph/api/resources/identityuserflow?view=graph-rest-beta) |
| Integrera med Azure DevOps | En [CI/CD-pipeline](deploy-custom-policies-devops.md) gör det enkelt att flytta kod mellan olika miljöer och säkerställer produktionsberedskap hela tiden.   |
| Integrera med Azure Monitor | [Granskningslogghändelser](view-audit-logs.md) behålls endast i sju dagar. [Integrera med Azure Monitor](azure-monitor.md) för att behålla loggarna för långvarig användning, eller integrera med siem-verktyg (Security Information and Event Management) från tredje part för att få insikter i din miljö. |
| Konfigurera aktiv avisering och övervakning | [Spåra användarbeteende](active-directory-b2c-custom-guide-eventlogger-appins.md) i Azure AD B2C med hjälp av Application Insights. |


## <a name="support-and-status-updates"></a>Support- och statusuppdateringar

Håll dig uppdaterad med tjänstens tillstånd och hitta supportalternativ.

|  |  |
|--|--|
| [Uppdateringar av tjänsten](https://azure.microsoft.com/updates/?product=active-directory-b2c) |  Håll dig uppdaterad med Azure AD B2C-produktuppdateringar och meddelanden. |
| [Microsoft Support](support-options.md) | Lämna in en supportbegäran för tekniska problem med Azure AD B2C. Support för fakturerings- och prenumerationshantering tillhandahålls utan kostnad. |
| [Azure-status](https://status.azure.com/status) | Visa aktuell hälsostatus för alla Azure-tjänster. |