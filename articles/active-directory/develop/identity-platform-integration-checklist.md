---
title: Metod tips för Microsoft Identity Platform | Azure
description: Lär dig mer om metod tips, rekommendationer och vanliga överblickar när du integrerar med Microsoft Identity Platform.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 1ce33e90cab1c5172cbf14470f24345d0446b0de
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200281"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Metod tips och rekommendationer för Microsoft Identity Platform

I den här artikeln beskrivs bästa praxis, rekommendationer och vanliga översikter vid integrering med Microsoft Identity Platform.  Den här check listan leder dig till en högkvalitativ och säker integrering. Granska listan regelbundet för att se till att du upprätthåller kvaliteten och säkerheten för appens integrering med identitets plattformen. Check listan är inte avsedd att granska hela programmet. Innehållet i check listan kan ändras när vi gör förbättringar av plattformen.

Om du precis har kommit igång kan du läsa dokumentationen om autentisering i [Microsoft Identity Platform](index.yml) för att lära dig grunderna för autentisering, program scenarier i Microsofts identitets plattform med mera.

Använd följande check lista för att se till att programmet är effektivt integrerat med [Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/).

## <a name="basics"></a>Grundläggande inställningar

|   |   |
|---|---|
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Läs och förstå [Microsofts plattforms principer](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Se till att ditt program följer de villkor som har utformats för att skydda användare och plattform. |

## <a name="ownership"></a>Kap

|   |   |
|---|---|
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Se till att informationen som är kopplad till det konto som du använde för att registrera och hantera appar är uppdaterad. |

## <a name="branding"></a>Anpassning

|   |   |
|---|---|
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Följ [rikt linjerna för anpassning av program](howto-add-branding-in-azure-ad-apps.md). |
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Ange ett beskrivande namn och en logo typ för ditt program. Den här informationen visas i [appens godkännande fråga](application-consent-experience.md). Kontrol lera att namnet och logo typen är representativa för företaget/produkten så att användarna kan fatta välgrundade beslut. Se till att du inte bryter mot varumärken. |

## <a name="privacy"></a>Sekretess

|   |   |
|---|---|
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Ange länkar till appens användnings villkor och sekretess policy. |

## <a name="security"></a>Säkerhet

|   |   |
|---|---|
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Hantera dina omdirigerings-URI: er: <ul><li>Behåll ägarskapet för alla dina omdirigerings-URI: er och se till att DNS-posterna är aktuella.</li><li>Använd inte jokertecken (*) i dina URI: er.</li><li>För Web Apps ser du till att alla URI: er är säkra och krypterade (till exempel med hjälp av https-scheman).</li><li>För offentliga klienter använder du plattformsspecifika omdirigerings-URI: er om tillämpligt (huvudsakligen för iOS och Android). Annars använder du omdirigerings-URI: er med hög slumpmässighet för att förhindra kollisioner när du ringer tillbaka till din app.</li><li>Om din app används från en isolerad webb agent kan du använda https://login.microsoftonline.com/common/oauth2/nativeclient.</li><li>Granska och rensa alla oanvända eller onödiga omdirigerings-URI: er regelbundet.</li></ul> |
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Om din app är registrerad i en katalog minimerar du och övervakar sedan listan över program registrerings ägare manuellt. |
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Aktivera inte stöd för det [OAuth2 implicita beviljade flödet](v2-oauth2-implicit-grant-flow.md) om det inte uttryckligen krävs. Lär dig mer om det giltiga scenariot [här](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Flytta bortom användar namn/lösen ord. Använd inte [ROPC (Resource Owner Password Credential Flow)](v2-oauth-ropc.md)som direkt hanterar användarnas lösen ord. Det här flödet kräver en hög grad av förtroende och användar exponering och bör endast användas när andra, säkrare flöden inte kan användas. Det här flödet behövs fortfarande i vissa scenarier (t. ex. DevOps), men tänk på att använda det för att införa begränsningar i ditt program.  För fler moderna metoder, Läs [autentiserings flöden och program scenarier](authentication-flows-app-scenarios.md).|
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Skydda och hantera dina konfidentiella autentiseringsuppgifter för webbappar, webb-API: er och daemon-appar. Använd [autentiseringsuppgifter för certifikat](active-directory-certificate-credentials.md), inte autentiseringsuppgifter för lösen ord (klient hemligheter). Om du måste använda autentiseringsuppgifter för lösen ord ska du inte ange det manuellt. Lagra inte autentiseringsuppgifter i koden eller config och Tillåt aldrig att de hanteras av människa. Använd om möjligt [hanterade identiteter för Azure-resurser](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) eller [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) för att lagra och regelbundet Rotera dina autentiseringsuppgifter. |
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Kontrol lera att ditt program begär minsta behörighet för behörighet. Fråga bara efter behörigheter som programmet absolut behöver, och endast när du behöver dem. Förstå de olika [typerna av behörigheter](v1-permissions-and-consent.md#types-of-permissions). Använd endast program behörigheter om det behövs. Använd delegerade behörigheter där det är möjligt. En fullständig lista över Microsoft Graph behörigheter finns i [referensen för behörighet](https://docs.microsoft.com/graph/permissions-reference). |
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Om du skyddar ett API med hjälp av Microsoft Identity Platform kan du noga tänka igenom de behörigheter som den ska visa. Överväg vad som är rätt kornig het för din lösning och vilka behörigheter som kräver administratörs medgivande. Sök efter förväntade behörigheter i inkommande token innan du fattar auktoriseringsbeslut. |

## <a name="implementation"></a>Implementering

|   |   |
|---|---|
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Använd moderna autentiserings lösningar (OAuth 2,0, [OpenID Connect](v2-protocols-oidc.md)) för att på ett säkert sätt logga in användare. |
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) |  Program mera inte direkt mot protokoll som OAuth 2,0 och Open ID. Använd i stället [Microsoft Authentication Library (MSAL)](msal-overview.md). MSAL-biblioteken kan på ett säkert sätt figursätta säkerhets protokoll i ett lättanvänt bibliotek, och du får inbyggt stöd för [villkorliga åtkomst](/azure/active-directory/conditional-access/overview) scenarier, [enkel inloggning (SSO)](/azure/active-directory/manage-apps/what-is-single-sign-on)och inbyggt stöd för cachelagring av token. Mer information finns i listan över [klient](reference-v2-libraries.md#microsoft-supported-client-libraries) bibliotek i Microsoft som stöds och [mellan bibliotek](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) och listan över [kompatibla klient bibliotek från tredje part](reference-v2-libraries.md#compatible-client-libraries).<br/><br/>Om du behöver manuell kod för autentiseringsprotokollen bör du följa en metod som [Microsoft sdl](https://www.microsoft.com/sdl/default.aspx). Var noga med säkerhets aspekterna i specifikationerna för varje protokoll.|
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) |  Migrera befintliga appar från [Azure Active Directory Authentication Library (ADAL)](active-directory-authentication-libraries.md) till [Microsoft Authentication Library](msal-overview.md). MSAL är Microsofts senaste identitets plattforms lösning och föredras som ADAL. Den är tillgänglig på .NET, Java Script, Android, iOS, macOS och finns även i offentlig för hands version för python och Java. Läs mer om att migrera [ADAL.net](msal-net-migration.md)-, [ADAL-. js](msal-compare-msal-js-and-adal-js.md)-och ADAL.net- [och iOS Broker](msal-net-migration-ios-broker.md) -appar.|
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) |  För Mobile Apps konfigurerar du varje plattform med hjälp av program registrerings upplevelsen. För att ditt program ska kunna dra nytta av Microsoft Authenticator eller Microsoft Företagsportal för enkel inloggning måste appen ha konfigurerat en "Broker-omdirigerings-URI". Detta gör att Microsoft kan returnera kontroll till programmet efter autentisering. När du konfigurerar varje plattform vägleder vi dig genom processen. Använd snabb starten för att hämta ett arbets exempel. Använd utjämnare och system webbvy när det är möjligt på iOS.|
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) |  I Web Apps eller webb-API: er, Behåll ett token-cache per konto.  För Web Apps bör token-cachen anges av konto-ID: t.  För webb-API: er ska kontot anges genom hashen för den token som används för att anropa API: et. MSAL.NET tillhandahåller anpassad cachelagring av token i .NET Framework-och .NET Core-underplattformar. Av säkerhets-och prestanda skäl är vår rekommendation att serialisera en cache per användare. Mer information finns i about [cache-serialisering för token](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).|
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Om de data som krävs för din app är tillgängliga via [Microsoft Graph](https://developer.microsoft.com/graph)kan du begära behörigheter för dessa data med hjälp av Microsoft Graph-slutpunkten i stället för enskilda API. |
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) |Titta inte på värdet för åtkomsttoken eller försök att parsa det som en klient.  De kan ändra värden, format eller till och med bli krypterade utan varning – Använd alltid id_token om klienten behöver lära sig något om användaren eller anropa Microsoft Graph.  Endast webb-API: er ska parsa åtkomsttoken (eftersom de är de som definierar formatet och ställer in krypterings nycklarna). |

## <a name="end-user-experience"></a>Slut användar upplevelse

|   |   |
|---|---|
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | [Förstå medgivande upplevelsen](application-consent-experience.md) och konfigurera delarna i appens godkännande fråga så att slutanvändare och administratörer har tillräckligt med information för att avgöra om de litar på din app. |
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Minimera antalet gånger som en användare måste ange inloggnings uppgifter när de använder din app genom att försöka utföra tyst autentisering (tyst token-hämtning) innan interaktiva flöden. |
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Använd inte "prompt = medgivande" för varje inloggning. Använd inte prompt = medgivande om du har fastställt att du behöver be om ytterligare behörigheter (till exempel om du har ändrat appens nödvändiga behörigheter). |
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | I förekommande fall kan du utöka ditt program med användar data. Att använda [Microsoft Graph API](https://developer.microsoft.com/graph) är ett enkelt sätt att göra detta. [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) -verktyget som kan hjälpa dig att komma igång. |
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Registrera den fullständiga uppsättningen behörigheter som din app kräver så att administratörerna enkelt kan ge sitt medgivande till sina klienter. Använd ett [stegvist godkännande](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) vid körning för att hjälpa användarna att förstå varför appen begär behörigheter som kan innebära eller förväxla användare när de begär det första starten. |
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementera en [ren enkel inloggnings upplevelse](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Det är en sekretess och ett säkerhets krav och ger en bättre användar upplevelse. |

## <a name="testing"></a>Testning

|   |   |
|---|---|
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Testa för [principer för villkorlig åtkomst](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) som kan påverka användarnas möjlighet att använda programmet. |
| ![Rute](./media/active-directory-integration-checklist/checkbox-two.svg) | Testa ditt program med alla möjliga konton som du planerar att stödja (till exempel arbets-eller skol konton, personliga Microsoft-konton, underordnade konton och suveräna konton). |

## <a name="additional-resources"></a>Ytterligare resurser

Utforska detaljerad information om v2.0:

* [Översikt över Microsoft Identity Platform (v 2.0)](v2-overview.md)
* [Referens för Microsoft Identity Platform-protokoll](active-directory-v2-protocols.md)
* [Referens för åtkomsttoken](access-tokens.md)
* [Referens för ID-token](id-tokens.md)
* [Referens för autentiserings bibliotek](reference-v2-libraries.md)
* [Behörigheter och medgivande i Microsoft Identity Platform](v2-permissions-and-consent.md)
* [Microsoft Graph-API](https://developer.microsoft.com/graph)
