---
title: Metodtips för Microsofts identitetsplattform | Azure
description: Lär dig mer om metodtips, rekommendationer och gemensamma förbiseenden när du integrerar med Microsofts identitetsplattform.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 56975cebbfe4f6dd6452c850c338d431faea27bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050499"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Bästa praxis och rekommendationer för Microsoft identity-plattformen

I den här artikeln beskrivs metodtips, rekommendationer och gemensamma förbiseenden när du integrerar med Microsofts identitetsplattform.  Denna checklista guidar dig till en högkvalitativ och säker integration. Granska listan regelbundet för att se till att du upprätthåller kvaliteten och säkerheten för appens integrering med identitetsplattformen. Checklistan är inte avsedd att granska hela din ansökan. Innehållet i checklistan kan komma att ändras när vi gör förbättringar av plattformen.

Om du precis har kommit igång kan du läsa dokumentationen till [Microsofts identitetsplattform](index.yml) för att lära dig mer om grunderna i autentisering, programscenarier i Microsofts identitetsplattform med mera.

Använd följande checklista för att se till att ditt program är effektivt integrerat med [Microsofts identitetsplattform](https://docs.microsoft.com/azure/active-directory/develop/).

## <a name="basics"></a>Grundläggande inställningar

|   |   |
|---|---|
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Läs och förstå [Microsofts plattformspolicyer](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Se till att ditt program följer de termer som beskrivs när de är utformade för att skydda användare och plattformen. |

## <a name="ownership"></a>Ägarskap

|   |   |
|---|---|
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Kontrollera att informationen som är kopplad till kontot som du använde för att registrera och hantera appar är uppdaterad. |

## <a name="branding"></a>Anpassning

|   |   |
|---|---|
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Följ [riktlinjerna för branding för applikationer](howto-add-branding-in-azure-ad-apps.md). |
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Ange ett meningsfullt namn och logotyp för ditt program. Den här informationen visas i [programmets samtyckesprompt](application-consent-experience.md). Se till att ditt namn och din logotyp är representativa för ditt företag/din produkt så att användarna kan fatta välgrundade beslut. Se till att du inte bryter mot några varumärken. |

## <a name="privacy"></a>Sekretess

|   |   |
|---|---|
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Tillhandahålla länkar till appens användarvillkor och sekretesspolicy. |

## <a name="security"></a>Säkerhet

|   |   |
|---|---|
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Hantera dina omdirigerings-URI:er: <ul><li>Underhåll ägarskapet för alla omdirigerings-URI:er och håll DNS-posterna uppdaterade för dem.</li><li>Använd inte jokertecken (*) i dina URI:er.</li><li>För webbappar kontrollerar du att alla URI:er är säkra och krypterade (till exempel med hjälp av https-scheman).</li><li>För offentliga klienter kan du använda plattformsspecifika omdirigerings-URI:er om tillämpligt (främst för iOS och Android). Annars kan du använda omdirigera URI:er med en stor mängd slumpmässighet för att förhindra kollisioner när du ringer tillbaka till din app.</li><li>Om din app används från en isolerad webbagent kan du använda `https://login.microsoftonline.com/common/oauth2/nativeclient`.</li><li>Granska och trimma alla oanvända eller onödiga omdirigerings-URI:er regelbundet.</li></ul> |
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Om din app är registrerad i en katalog, minimera och manuellt övervaka listan över app registreringsägare. |
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Aktivera inte stöd för [OAuth2 implicita bidragsflödet](v2-oauth2-implicit-grant-flow.md) om det inte uttryckligen krävs. Läs mer om det giltiga scenariot [här](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Gå längre än användarnamn/lösenord. Använd inte [resursägare lösenordsautentiseringsflöde (ROPC),](v2-oauth-ropc.md)som direkt hanterar användarnas lösenord. Detta flöde kräver en hög grad av förtroende och användarexponering och bör endast användas när andra, säkrare, flöden inte kan användas. Det här flödet behövs fortfarande i vissa scenarier (som DevOps), men se upp för att använda det kommer att medföra begränsningar för ditt program.  Mer moderna metoder finns i [Autentiseringsflöden och programscenarier](authentication-flows-app-scenarios.md).|
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Skydda och hantera dina konfidentiella appuppgifter för webbappar, webb-API:er och daemonapps. Använd [certifikatuppgifter](active-directory-certificate-credentials.md), inte lösenordsuppgifter (klienthemligheter). Om du måste använda en lösenordsautentiseringsautentisering ska du inte ange den manuellt. Lagra inte autentiseringsuppgifter i kod eller konfiguration och tillåt dem aldrig att hanteras av människor. Använd om möjligt [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) eller [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) för att lagra och regelbundet rotera dina autentiseringsuppgifter. |
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Kontrollera att programmet begär minst behörighet. Be bara om behörigheter som ditt program absolut behöver, och endast när du behöver dem. Förstå de olika [typerna av behörigheter](v2-permissions-and-consent.md#permission-types). Använd endast programbehörigheter om det behövs. använda delegerade behörigheter där det är möjligt. En fullständig lista över behörigheter i Microsoft Graph finns i den här [behörighetsreferensen](https://docs.microsoft.com/graph/permissions-reference). |
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Om du skyddar ett API med Microsofts identitetsplattform bör du noggrant tänka igenom de behörigheter som det ska visa. Tänk på vad som är rätt granularitet för din lösning och vilka behörigheter kräver administratörsmedgivande. Sök efter förväntade behörigheter i inkommande token innan du fattar några auktoriseringsbeslut. |

## <a name="implementation"></a>Implementering

|   |   |
|---|---|
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Använd moderna autentiseringslösningar (OAuth 2.0, [OpenID Connect)](v2-protocols-oidc.md)för att logga in användare på ett säkert sätt. |
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) |  Programmera inte direkt mot protokoll som OAuth 2.0 och Open ID. Använd i stället [Microsoft Authentication Library (MSAL)](msal-overview.md). MSAL-biblioteken radbryts säkert i säkerhetsprotokoll i ett lättanvänt bibliotek, och du får inbyggt stöd för scenarier för [villkorlig åtkomst,](/azure/active-directory/conditional-access/overview) enhetsomfattande [enkel inloggning (SSO)](/azure/active-directory/manage-apps/what-is-single-sign-on)och inbyggt stöd för tokencachelagring. Mer information finns i listan över [Microsoft-stödda klientbibliotek](reference-v2-libraries.md#microsoft-supported-client-libraries) och [middleware-bibliotek](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) och listan över [kompatibla klientbibliotek från tredje part](reference-v2-libraries.md#compatible-client-libraries).<br/><br/>Om du måste handkoda autentiseringsprotokollen bör du följa en metod som [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Var uppmärksam på säkerhetsaspekterna i standardspecifikationerna för varje protokoll.|
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) |  Migrera befintliga appar från [ADAL (Azure Active Directory Authentication Library)](../azuread-dev/active-directory-authentication-libraries.md) till [Microsoft Authentication Library](msal-overview.md). MSAL är Microsofts senaste lösning på identitetsplattform och är att föredra framför ADAL. Den är tillgänglig på .NET, JavaScript, Android, iOS, macOS och är också i offentlig förhandsversion för Python och Java. Läs mer om att migrera [ADAL.NET,](msal-net-migration.md) [ADAL.js](msal-compare-msal-js-and-adal-js.md)och [ADAL.NET- och iOS-mäklarappar.](msal-net-migration-ios-broker.md)|
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) |  Konfigurera varje plattform med hjälp av programregistreringsupplevelsen för mobilappar. För att ditt program ska kunna dra nytta av Microsoft Authenticator eller Microsoft Company Portal för enkel inloggning behöver din app en "uri för mäklaromdirigering" konfigurerad. På så sätt kan Microsoft returnera kontrollen till ditt program efter autentisering. När du konfigurerar varje plattform guidar appregistreringsupplevelsen dig genom processen. Använd snabbstarten för att hämta ett fungerande exempel. På iOS, använd mäklare och system webview när det är möjligt.|
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) |  I webbappar eller webb-API:er behåller du en tokencache per konto.  För webbappar ska tokencachen knappas in av konto-ID:t.  För webb-API:er ska kontot knappas in av hash-värdet för den token som används för att anropa API:et. MSAL.NET tillhandahåller anpassad tokencacheseriering i underplatformulären .NET Framework och .NET Core. Av säkerhets- och prestandaskäl är vår rekommendation att serialisera en cache per användare. Mer information finns i om [serialisering av tokencache](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).|
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Om de data som appen kräver är tillgängliga via [Microsoft Graph](https://developer.microsoft.com/graph)begär du behörigheter för dessa data med hjälp av Microsoft Graph-slutpunkten i stället för det enskilda API:et. |
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) |Titta inte på åtkomsttokenvärdet eller försök att tolka det som en klient.  De kan ändra värden, format eller till och med bli krypterade utan förvarning - använd alltid id_token om din klient behöver lära sig något om användaren eller ringa Microsoft Graph.  Endast webb-API:er ska tolka åtkomsttoken (eftersom det är de som definierar formatet och anger krypteringsnycklarna). |

## <a name="end-user-experience"></a>Upplevelse för slutanvändaren

|   |   |
|---|---|
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | [Förstå samtyckesupplevelsen](application-consent-experience.md) och konfigurera delar av appens samtyckesfråga så att slutanvändare och administratörer har tillräckligt med information för att avgöra om de litar på din app. |
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimera antalet gånger en användare behöver ange inloggningsuppgifter när du använder din app genom att försöka med tyst autentisering (tyst tokeninsamling) innan interaktiva flöden. |
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Använd inte "prompt=consent" för varje inloggning. Använd endast prompt=consent om du har fastställt att du behöver be om medgivande om ytterligare behörigheter (till exempel om du har ändrat appens behörigheter). |
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | I förekommande fall, berika ditt program med användardata. Att använda [Microsoft Graph API](https://developer.microsoft.com/graph) är ett enkelt sätt att göra detta. [Graph Explorer-verktyget](https://developer.microsoft.com/graph/graph-explorer) som kan hjälpa dig att komma igång. |
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Registrera den fullständiga uppsättningen behörigheter som din app kräver så att administratörer enkelt kan bevilja samtycke till sin klientorganisation. Använd [inkrementellt medgivande](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) vid körning för att hjälpa användarna att förstå varför din app begär behörigheter som kan beröra eller förvirra användare när de begärs vid första start. |
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementera en [ren engångsdet-ut-upplevelse](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Det är en integritet och ett säkerhetskrav, och ger en bra användarupplevelse. |

## <a name="testing"></a>Testning

|   |   |
|---|---|
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Testa för principer för [villkorlig åtkomst](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) som kan påverka användarnas möjlighet att använda ditt program. |
| ![kryssruta](./media/active-directory-integration-checklist/checkbox-two.svg) | Testa ditt program med alla möjliga konton som du planerar att stödja (till exempel arbets- eller skolkonton, personliga Microsoft-konton, barnkonton och suveräna konton). |

## <a name="additional-resources"></a>Ytterligare resurser

Utforska detaljerad information om v2.0:

* [Microsoft-identitetsplattform (v2.0-översikt)](v2-overview.md)
* [Referens för Microsoft-identitetsplattformsprotokoll](active-directory-v2-protocols.md)
* [Referens för åtkomsttoken](access-tokens.md)
* [Referens för ID-token](id-tokens.md)
* [Referens för autentiseringsbibliotek](reference-v2-libraries.md)
* [Behörigheter och medgivande på Microsofts identitetsplattform](v2-permissions-and-consent.md)
* [Microsoft Graph-API](https://developer.microsoft.com/graph)
