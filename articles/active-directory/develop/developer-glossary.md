---
title: Ordlista för utvecklare för Azure Active Directory | Microsoft Docs
description: En lista över villkor för vanliga begrepp för utvecklare av Azure Active Directory och funktioner.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/16/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol
ms.openlocfilehash: ce2f64c50818a17d199da896b42b2218e6637d07
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2019
ms.locfileid: "55097868"
---
# <a name="azure-active-directory-developer-glossary"></a>Ordlista för utvecklare för Azure Active Directory

Den här artikeln innehåller definitioner för några av de viktigaste Azure Active Directory (AD) developer begreppen, som är användbara när du lär dig om programutveckling för Azure AD.

## <a name="access-token"></a>åtkomsttoken

En typ av [säkerhetstoken](#security-token) utfärdats av en [auktoriseringsservern](#authorization-server), och används av en [klientprogram](#client-application) för att komma åt en [skyddade resursservern](#resource-server). Vanligtvis i form av en [JSON Web Token (JWT)][JWT], token återspeglar det tillstånd som beviljats till klienten med den [resursägaren](#resource-owner), för en begärda åtkomstnivå. Token som innehåller alla tillämpliga [anspråk](#claim) om ämne, aktiverar klientprogrammet att använda det som en form av autentiseringsuppgifter vid åtkomst till en viss resurs. Detta eliminerar också behovet av resursägaren att exponera autentiseringsuppgifterna till klienten.

Åtkomsttoken är ibland kallas ”användare + App” eller ”Appspecifika”, beroende på de autentiseringsuppgifter som representeras. Till exempel när ett klientprogram använder den:

* [”Auktoriseringskod” auktoriseringsbeviljande](#authorization-grant), autentiserar användaren först som ägare till resursen delegera behörighet att klienten till resursen. Klienten autentiserar efteråt när hämta åtkomsttoken. Token kan ibland kallas mer specifikt en ”användare + App”-token som representerar både användaren som behörighet klientprogrammet och programmet.
* [”Klientautentiseringsuppgifter” auktoriseringsbeviljande](#authorization-grant), klienten ger den enda autentiseringen fungerar utan på resurs-ägare autentisering/auktorisering, så att token kan ibland kallas en ”Appspecifika”-token.

Se [tokenreferens för Azure-AD] [ AAD-Tokens-Claims] för mer information.

## <a name="application-id-client-id"></a>program-id (klient-id)

Unik identifierare Azure AD-problem till en programregistrering som identifierar ett visst program och tillhörande konfigurationer. Den här program-id ([klient-id](https://tools.ietf.org/html/rfc6749#page-15)) används när du utför autentisering begär och är tillhandahålls autentisering-biblioteken i tid. Program-id (klient-id) är inte en hemlighet.

## <a name="application-manifest"></a>programmanifest

En funktion som tillhandahålls av den [Azure-portalen][AZURE-portal], som producerar en JSON-representation av programmets identitet konfiguration, används som en mekanism för att uppdatera den är kopplad till [ Programmet] [ AAD-Graph-App-Entity] och [ServicePrincipal] [ AAD-Graph-Sp-Entity] entiteter. Se [förstå programmanifestet för Azure Active Directory] [ AAD-App-Manifest] för mer information.

## <a name="application-object"></a>programobjekt

När du registrera dig/uppdatera ett program i den [Azure-portalen][AZURE-portal], portalen skapat/uppdaterat ett programobjekt och en motsvarande [tjänstens huvudnamnsobjekt](#service-principal-object) för den klienten. Programobjektet *definierar* programmet användarens identitet configuration globalt (för alla klienter med åtkomst), vilket ger en mall som dess motsvarande huvudnamn service-objekt är  *härledda* för användning lokalt under körning (i en specifik klient).

Mer information finns i [program och tjänstobjekt][AAD-App-SP-Objects].

## <a name="application-registration"></a>Programregistrering

För att tillåta att ett program att integrera med och delegera Identity and Access Management-funktioner till Azure AD, måste den vara registrerad med en Azure AD [klient](#tenant). När du registrerar ditt program med Azure AD kan tillhandahåller du en identity-konfiguration för ditt program, så att den kan integreras med Azure AD och använder funktioner som:

* Stabil hantering av enkel inloggning med hjälp av Azure AD Identity Management och [OpenID Connect] [ OpenIDConnect] protokollimplementering
* Asynkrona åtkomst till [skyddade resurser](#resource-server) av [klientprogram](#client-application), via Azure AD OAuth 2.0 [auktoriseringsservern](#authorization-server) implementering
* [Ramverket för medgivande](#consent) för att hantera klientåtkomst till skyddade resurser, baserat på ägarauktorisering för resursen.

Se [integrera program med Azure Active Directory] [ AAD-Integrating-Apps] för mer information.

## <a name="authentication"></a>autentisering

Act angrips en part för giltiga autentiseringsuppgifter som ligger till grund för att skapa ett säkerhetsobjekt som ska användas för identitets- och åtkomstkontroll. Under en [OAuth2 auktoriseringsbeviljande](#authorization-grant) exempelvis part autentisering fylls rollen antingen [resursägaren](#resource-owner) eller [klientprogram](#client-application), beroende på den bevilja används.

## <a name="authorization"></a>Auktorisering

Åtgärden för att bevilja en autentiserad huvudnamn behörighet att göra något. Det vinns två huvudsakliga typer i Azure AD-programmeringsmodellen:

* Under en [OAuth2 auktoriseringsbeviljande](#authorization-grant) flow: när den [resursägaren](#resource-owner) beviljar behörighet att den [klientprogram](#client-application), vilket gör att klientdatorer för åtkomst till resursen ägarens resurser.
* Under resursåtkomst av klienten: som implementeras av den [resursservern](#resource-server)med hjälp av den [anspråk](#claim) värden finns i den [åtkomsttoken](#access-token) att göra besluten om åtkomstkontroll baserat på dem.

## <a name="authorization-code"></a>auktoriseringskod

En kort livslängd ”token” till en [klientprogram](#client-application) av den [auktoriseringsslutpunkt](#authorization-endpoint), som en del av flödet ”auktoriseringskod”, en av de fyra OAuth2 [auktorisering ger](#authorization-grant). Kod som returneras till klientprogrammet som svar på autentisering av en [resursägaren](#resource-owner), som anger resursägaren har delegerats behörighet att komma åt de begärda resurserna. Som en del av flödet, koden senare användas för en [åtkomsttoken](#access-token).

## <a name="authorization-endpoint"></a>auktoriseringsslutpunkt

En av slutpunkterna som implementeras av den [auktoriseringsservern](#authorization-server)används för att interagera med den [resursägaren](#resource-owner) för att tillhandahålla en [auktoriseringsbeviljande](#authorization-grant) under en OAuth2 flöde för att bevilja tillstånd. Beroende på authorization grant flödet som används, det faktiska beviljandet som tillhandahålls kan variera, inklusive en [auktoriseringskod](#authorization-code) eller [säkerhetstoken](#security-token).

Se OAuth2-specifikationen [typer av auktoriseringsbeviljanden] [ OAuth2-AuthZ-Grant-Types] och [auktoriseringsslutpunkt] [ OAuth2-AuthZ-Endpoint] avsnitt och [OpenIDConnect specifikationen] [ OpenIDConnect-AuthZ-Endpoint] för mer information.

## <a name="authorization-grant"></a>auktoriseringsbeviljande

En autentiseringsuppgift som representerar den [resource ägarens](#resource-owner) [auktorisering](#authorization) att få åtkomst till dess skyddade resurser som tilldelas en [klientprogram](#client-application). Ett klientprogram kan använda någon av de [fyra bevilja typer som definieras av ramverket för OAuth2-auktorisering] [ OAuth2-AuthZ-Grant-Types] att hämta en tilldelning, beroende på typen/klientkrav: ”auktoriseringskodbeviljande”, ”klient beviljande av autentiseringsuppgifter för ”,” implicit beviljande ”och” resource resursägarens lösenordsautentiseringsuppgifter ”. Autentiseringsuppgifterna som returneras till klienten är antingen en [åtkomsttoken](#access-token), eller en [auktoriseringskod](#authorization-code) (utväxlats senare för en åtkomsttoken) beroende på vilken typ av auktoriseringsbeviljande som används.

## <a name="authorization-server"></a>auktoriseringsservern

Enligt definitionen i den [OAuth2 auktorisering Framework][OAuth2-Role-Def], servern som är ansvarig för att utfärda åtkomst-token till den [klienten](#client-application) efter autentisering i [resursägaren](#resource-owner) och för att hämta dess tillstånd. En [klientprogram](#client-application) interagerar med auktoriseringsservern vid körning via dess [auktorisering](#authorization-endpoint) och [token](#token-endpoint) slutpunkter, i enlighet med OAuth2 definierats [auktoriseringsbeviljanden](#authorization-grant).

När det gäller integrering av Azure AD, Azure AD implementerar rollen auktorisering för Azure AD-program och Microsoft service API: er, till exempel [Microsoft Graph API: er][Microsoft-Graph].

## <a name="claim"></a>anspråk

En [säkerhetstoken](#security-token) innehåller anspråk, som ger intyg om en entitet (till exempel en [klientprogram](#client-application) eller [resursägaren](#resource-owner)) till en annan entitet (till exempel [resursservern](#resource-server)). Anspråk är namn/värde-par som vidarebefordrar fakta om token område (till exempel det säkerhetsobjekt som autentiserades via den [auktoriseringsservern](#authorization-server)). Anspråk som finns i en viss token är beroende av flera variabler, inklusive vilken typ av token, vilken typ av autentiseringsuppgift som används för att autentisera ämne, programkonfiguration, osv.

Se [tokenreferens för Azure AD] [ AAD-Tokens-Claims] för mer information.

## <a name="client-application"></a>Klientprogram

Enligt definitionen i den [OAuth2 auktorisering Framework][OAuth2-Role-Def], ett program som gör skyddade resursbegäranden för den [resursägaren](#resource-owner). Termen ”klient” innebär inte någon särskild maskinvaruegenskaper på implementering (till exempel om programmet körs på en server, en stationär dator eller andra enheter).

Ett klientprogram begär [auktorisering](#authorization) från en resursägare att delta i en [OAuth2 auktoriseringsbeviljande](#authorization-grant) flödar och kan komma åt API: er/data åt ägare till resursen. Ramverk för OAuth2-auktorisering [definierar två typer av klienter][OAuth2-Client-Types], ”konfidentiellt” och ”offentliga”, baserat på klientens förmåga att upprätthålla sekretessen för autentiseringsuppgifterna. Program kan ha en [webbklienten (konfidentiellt)](#web-client) som körs på en webbserver, en [native client (offentlig)](#native-client) installerats på en enhet eller en [användar-agent-baserad klient (offentlig)](#user-agent-based-client)som körs i enhetens webbläsare.

## <a name="consent"></a>Medgivande

Processen för en [resursägaren](#resource-owner) beviljar behörighet att en [klientprogram](#client-application), för att komma åt skyddade resurser under specifika [behörigheter](#permissions), för den resursägaren. Beroende på vilka behörigheter som begärs av klienten, ombeds Ingen administratör eller användare medgivande till att tillåta åtkomst till deras organisation/person data respektive. Observera att i en [flera innehavare](#multi-tenant-application) scenario, programmets [tjänstens huvudnamn](#service-principal-object) också registreras i klientorganisationen för consenting användaren.

Se [ramverket för medgivande](consent-framework.md) för mer information.

## <a name="id-token"></a>ID-token

En [OpenID Connect] [ OpenIDConnect-ID-Token] [säkerhetstoken](#security-token) tillhandahålls av en [auktorisering server](#authorization-server) [auktoriseringsslutpunkt](#authorization-endpoint), som innehåller [anspråk](#claim) hör till autentisering av en slutanvändare [resursägaren](#resource-owner). Som ett åtkomsttoken, representeras även ID-token som ett digitalt signerat [JSON Web Token (JWT)][JWT]. Till skillnad från en åtkomsttoken dock en ID-token anspråk används inte för syften som är relaterade till åtkomst till resurser och åtkomstkontroll specifikt.

Se [tokenreferens för Azure AD] [ AAD-Tokens-Claims] för mer information.

## <a name="microsoft-identity-platform"></a>Microsoft identitetsplattform

Microsoft Identity-plattformen är en utveckling av identitetstjänsten och utvecklingsplattformen Azure Active Directory (Azure AD). Den hjälper utvecklare att bygga program som loggar in alla Microsoft-identiteter, får tokens för att anropa Microsoft Graph, andra Microsoft API:er eller API:er som utvecklare har byggt. Det är en fullständig plattform som består av en autentiseringstjänst, bibliotek, programregistrering och konfiguration, fullständig utvecklardokumentation, kodexempel och annat innehåll för utvecklare. Microsoft Identity-plattformen stöder branschstandardprotokoll som OAuth 2.0 och OpenID Connect. Se [om Microsoft identity-plattformen](about-microsoft-identity-platform.md) för mer information.

## <a name="multi-tenant-application"></a>program med flera innehavare

En klass av program som kan logga in och [godkänna](#consent) av användare som har etablerats i Azure AD [klient](#tenant), inklusive klienter än den där klienten är registrerad. [Inbyggd klient](#native-client) program är flera innehavare som standard medan [webbklienten](#web-client) och [webb-resurs/API](#resource-server) program möjlighet att välja mellan en eller flera innehavare. Däremot skulle ett webbprogram som är registrerad som en enda klient endast tillåta inloggningar från användarkonton som etablerats i samma klientorganisation som det där programmet har registrerats.

Se [logga in valfri Azure AD-användare med programmönstret för flera innehavare] [ AAD-Multi-Tenant-Overview] för mer information.

## <a name="native-client"></a>Inbyggd klient

En typ av [klientprogram](#client-application) som internt är installerad på en enhet. Eftersom all kod körs på en enhet, betraktas den som en ”offentliga” klient på grund av att det inte att lagra autentiseringsuppgifter privat/konfidentiellt. Se [OAuth2 klienten typer och -profiler] [ OAuth2-Client-Types] för mer information.

## <a name="permissions"></a>behörigheter

En [klientprogram](#client-application) får åtkomst till en [resursservern](#resource-server) genom att deklarera behörighetsbegäranden. Det finns två typer:

* ”Delegerad” behörighet, vilket anger [omfångsbaserad](#scopes) komma åt med delegerad auktorisering från den inloggade [resursägaren](#resource-owner), presenteras för resursen vid körning som [”scp” anspråk](#claim) i klientens [åtkomsttoken](#access-token).
* ”Program” behörighet, vilket anger [rollbaserad](#roles) komma åt med de klientprogram autentiseringsuppgifter/identitet, presenteras för resursen vid körning som [”roller” anspråk](#claim) i klientens åtkomst-token.

De också lyfta fram under den [godkänna](#consent) processen, vilket ger administratören eller resursägaren möjlighet att bevilja/neka klientåtkomst till resurser i deras klienter.

Begäranden om behörighet har konfigurerats på sidan ”program” / ”inställningar” fliken i den [Azure-portalen][AZURE-portal], under ”nödvändiga behörigheter”, genom att välja önskad ”delegerade behörigheter” och ”-program Behörigheter ”(det senare krävs medlemskap i rollen som Global administratör). Eftersom en [offentlig klient](#client-application) inte kan på ett säkert sätt att upprätthålla autentiseringsuppgifter, det kan bara begära delegerade behörigheter, medan en [konfidentiell klient](#client-application) har möjlighet att begära både delegerad och program behörigheter. Klientens [programobjektet](#application-object) lagrar deklarerade behörigheterna i dess [requiredResourceAccess egenskapen][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>resursägaren

Enligt definitionen i den [OAuth2 auktorisering Framework][OAuth2-Role-Def], en enhet som kan bevilja åtkomst till en skyddad resurs. Om resursägaren är en person, kallas det en slutanvändare. Till exempel när en [klientprogram](#client-application) vill komma åt en användares postlåda via den [Microsoft Graph API][Microsoft-Graph], det krävs behörighet från resursägaren av den postlåda.

## <a name="resource-server"></a>resursservern

Enligt definitionen i den [OAuth2 auktorisering Framework][OAuth2-Role-Def], en server som är värd för skyddad resurser, kan ta emot och svara på skyddade resursbegäranden av [klienten program](#client-application) som använder en [åtkomsttoken](#access-token). Även känt som en skyddad resurs server eller resursprogrammet.

Resursservern visar API: er och tillämpar åtkomst till dess skyddade resurser via [scope](#scopes) och [roller](#roles), med hjälp av OAuth 2.0-auktorisering ramverket. Exempel: Azure AD Graph-API som ger åtkomst till Azure AD-klient-data och Office 365 API: er som ger åtkomst till data, till exempel e-post och kalender. Båda dessa är också tillgängliga via den [Microsoft Graph API][Microsoft-Graph].

Precis som ett klientprogram resource programmets identitet konfiguration har upprättats [registrering](#application-registration) tillhandahåller både programmet och tjänstens huvudnamnsobjekt i en Azure AD-klient. Vissa Microsoft tillhandahåller API: er, till exempel Azure AD Graph-API har redan registrerat tjänstens huvudnamn som är tillgänglig i alla klienter under etableringen.

## <a name="roles"></a>roles

Som [scope](#scopes), roller gör det möjligt för en [resursservern](#resource-server) att styra åtkomst till dess skyddade resurser. Det finns två typer: en ”användare”-roll implementerar rollbaserad åtkomstkontroll för användare/grupper som kräver åtkomst till resursen, medan en ”program”-roll som implementerar samma för [klientprogram](#client-application) som kräver åtkomst.

Roller är resurs-definierade strängar (till exempel ”telekommunikation godkännare”, ”skrivskyddad”, ”Directory.ReadWrite.All”), hanteras i den [Azure-portalen] [ AZURE-portal] via resursens [program manifest](#application-manifest), och lagras i resursens [appRoles egenskapen][AAD-Graph-Sp-Entity]. Azure-portalen används också för att tilldela användare till roller ”användare” och konfigurera klienten [programbehörigheter](#permissions) att få åtkomst till en roll ”program”.

En detaljerad beskrivning av de programroller som exponeras av Azure AD Graph API finns i [Behörighetsomfattningar för Graph API][AAD-Graph-Perm-Scopes]. En stegvis implementering-exempel finns i [hantera åtkomst med RBAC och Azure portal][AAD-RBAC].

## <a name="scopes"></a>scopes

Som [roller](#roles), scope gör det möjligt för en [resursservern](#resource-server) att styra åtkomst till dess skyddade resurser. Omfång används för att implementera [omfångsbaserad] [ OAuth2-Access-Token-Scopes] åtkomstkontroll för en [klientprogram](#client-application) som har givits delegerad åtkomst till resursen av respektive ägare.

Scope är resurs-definierade strängar (till exempel ”Mail.Read”, ”Directory.ReadWrite.All”), som hanteras i den [Azure-portalen] [ AZURE-portal] via resursens [programmanifestet](#application-manifest), och lagras i resursens [oauth2Permissions egenskapen][AAD-Graph-Sp-Entity]. Azure-portalen används också för att konfigurera klientprogram [delegerade behörigheter](#permissions) att komma åt ett omfång.

En bästa praxis namngivningskonvention, är att använda ett format som ”resource.operation.constraint”. En detaljerad beskrivning av omfång som exponeras av Azure AD Graph API finns i [Behörighetsomfattningar för Graph API][AAD-Graph-Perm-Scopes]. Scope som visas av Office 365-tjänster, se [behörighetsreferens för Office 365 API][O365-Perm-Ref].

## <a name="security-token"></a>säkerhetstoken

Ett signerat dokument som innehåller anspråk, till exempel en OAuth2-token eller SAML 2.0-kontroll. För en OAuth2 [auktoriseringsbeviljande](#authorization-grant), en [åtkomsttoken](#access-token) (OAuth2) och en [ID-Token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) typer av säkerhetstoken som implementeras som en [JSON Webb-Token (JWT)][JWT].

## <a name="service-principal-object"></a>tjänstens huvudnamnsobjekt

När du registrera dig/uppdatera ett program i den [Azure-portalen][AZURE-portal], portalen skapat/uppdaterat både en [programobjektet](#application-object) och en motsvarande tjänstens huvudnamnsobjekt för den klienten. Programobjektet *definierar* programmets identitet konfiguration globalt (på alla klienter där det associerade programmet har beviljats åtkomst) och är mallen som dess motsvarande huvudnamn för tjänsten objekt är *härledda* för användning lokalt under körning (i en specifik klient).

Mer information finns i [program och tjänstobjekt][AAD-App-SP-Objects].

## <a name="sign-in"></a>inloggning

Processen för en [klientprogram](#client-application) initierar slutanvändarautentisering och samla in relaterade tillstånd, för att förvärva en [säkerhetstoken](#security-token) och konfigurera program till det aktuella tillståndet. Tillstånd kan omfatta artefakter, till exempel information om användarprofiler och information som härleds från tokenanspråken.

Funktionen logga in i ett program används vanligtvis för att implementera enkel inloggning (SSO). Det kan också föregås av en ”registrering”-funktion som startpunkt för en användare att få åtkomst till ett program (vid första inloggningen). Registrera dig funktionen används för att samla in och bevara ytterligare tillstånd som är specifika för användaren och kan kräva [användarmedgivande](#consent).

## <a name="sign-out"></a>utloggning

Processen för unauthenticating en slutanvändare, koppla från användarens tillstånd som associeras med den [klientprogram](#client-application) session under [inloggning](#sign-in)

## <a name="tenant"></a>klient

En instans av Azure AD-katalog kallas för en Azure AD-klient. Den tillhandahåller flera funktioner, inklusive:

* en registry-tjänsten för integrerade program
* autentisering av användarkonton och registrerade program
* REST-slutpunkter som krävs för att stödja olika protokoll, inklusive OAuth2 och SAML, inklusive den [auktoriseringsslutpunkt](#authorization-endpoint), [tokenslutpunkten](#token-endpoint) och ”vanliga” slutpunkten som används av [ program för flera innehavare](#multi-tenant-application).

Azure AD-klienter kan skapas/som är associerade med Azure och Office 365-prenumerationer under registrering, tillhandahåller identitets- och åtkomsthantering funktioner för prenumerationen. Azure-prenumerationsadministratörer kan också skapa ytterligare Azure AD-klienter via Azure portal. Se [skaffa en Azure Active Directory-klient] [ AAD-How-To-Tenant] mer information om de olika sätt du kan få åtkomst till en klient. Se [hur Azure-prenumerationer är associerade med Azure Active Directory] [ AAD-How-Subscriptions-Assoc] information om relationen mellan prenumerationer och en Azure AD-klient.

## <a name="token-endpoint"></a>tokenslutpunkt

En av slutpunkterna som implementeras av den [auktoriseringsservern](#authorization-server) till support OAuth2 [auktoriseringsbeviljanden](#authorization-grant). Beroende på bevilja, den kan användas till att hämta en [åtkomsttoken](#access-token) (och relaterade ”uppdatera” token) till en [klienten](#client-application), eller [ID-token](#ID-token) när det används med den [OpenID Ansluta] [ OpenIDConnect] protokoll.

## <a name="user-agent-based-client"></a>Användar-agent-baserad klient

En typ av [klientprogram](#client-application) som hämtar kod från en webbserver och kör inom en användaragent (till exempel en webbläsare), till exempel en ensidesapplikation (SPA). Eftersom all kod körs på en enhet, betraktas den som en ”offentliga” klient på grund av att det inte att lagra autentiseringsuppgifter privat/konfidentiellt. Mer information finns i [OAuth2 klienten typer och -profiler][OAuth2-Client-Types].

## <a name="user-principal"></a>UPN

Liknar det sätt som en tjänstens huvudnamnsobjekt som används för att representera en programinstans, ett huvudnamn användarobjekt är en annan typ av säkerhetsobjekt, som representerar en användare. Azure AD Graph [användarentiteten] [ AAD-Graph-User-Entity] definierar schemat för ett användarobjekt, inklusive användarrelaterade egenskaper, till exempel förnamn, efternamn, användarens huvudnamn, directory rollmedlemskap osv. Detta ger Användarkonfiguration för identitet för Azure AD för att upprätta en UPN vid körning. Huvudanvändaren används för att representera en autentiserad användare för enkel inloggning, spela in [godkänna](#consent) delegering, vilket gör besluten om åtkomstkontroll och så vidare.

## <a name="web-client"></a>webbklienten

En typ av [klientprogram](#client-application) som utför all kod på en webbserver och kan fungera som en ”konfidentiellt” klient genom att på ett säkert sätt lagra sina autentiseringsuppgifter på servern. Mer information finns i [OAuth2 klienten typer och -profiler][OAuth2-Client-Types].

## <a name="next-steps"></a>Nästa steg

Den [utvecklarhandboken för Azure AD] [ AAD-Dev-Guide] är landningssidan som ska användas för alla Azure AD-utveckling-relaterade ämnen, t.ex. en översikt över [programintegrering] [ AAD-How-To-Integrate] och grunderna i [Azure AD-autentisering och autentisering som stöds scenarier][AAD-Auth-Scenarios]. Du kan också hitta kodexempel och självstudier om hur du får igång snabbt på [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp att förfina och utforma det här innehållet, inklusive förfrågningar efter nya definitioner eller uppdatera befintliga!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
