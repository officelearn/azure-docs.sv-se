---
title: Ordlista för microsoft-identitetsplattformsutvecklare | Azure
description: En lista med termer för vanliga koncept och funktioner för utvecklare av Microsoft identity platform-utvecklare.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 12/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.openlocfilehash: ce98d2db86c87ac6aa8fa4872bc076714467d32f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263054"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Ordlista för microsoft-identitetsplattformsutvecklare

Den här artikeln innehåller definitioner för några av de viktigaste utvecklarbegreppen och terminologin, som är användbara när du lär dig mer om programutveckling med Hjälp av Microsofts identitetsplattform.

## <a name="access-token"></a>åtkomsttoken

En typ av [säkerhetstoken](#security-token) som utfärdats av en [auktoriseringsserver](#authorization-server)och som används av ett [klientprogram](#client-application) för att komma åt en [skyddad resursserver](#resource-server). Vanligtvis i form av en [JSON Web Token (JWT)][JWT]förkroppsligar token den auktorisering som beviljas klienten av [resursägaren](#resource-owner), för en begärd åtkomstnivå. Token innehåller alla tillämpliga [anspråk](#claim) om ämnet, vilket gör att klientprogrammet kan använda det som en form av autentiseringsuppgifter när du öppnar en viss resurs. Detta eliminerar också behovet av att resursägaren exponerar autentiseringsuppgifter för klienten.

Åtkomsttoken kallas ibland "User+App" eller "Endast app", beroende på vilka autentiseringsuppgifter som representeras. Till exempel när ett klientprogram använder:

* ["Auktoriseringskod" auktoriseringsbidrag](#authorization-grant), slutanvändaren autentiserar först som resursägare, delegerar auktorisering till klienten för att komma åt resursen. Klienten autentiserar efteråt när åtkomsttoken hämtas. Token kan ibland refereras till mer specifikt som en "User+App"-token, eftersom den representerar både användaren som godkände klientprogrammet och programmet.
* ["Klientautentiseringsuppgifter" auktoriseringsbehörighet](#authorization-grant), klienten tillhandahåller den enda autentiseringen, som fungerar utan resursägarens autentisering/auktorisering, så token kan ibland kallas en "Endast app"-token.

Mer information finns i Tokenreferens för [Microsoft-identitetsplattform.][AAD-Tokens-Claims]

## <a name="application-id-client-id"></a>program-ID (klient-ID)

Den unika identifieraren Azure AD-problem med en programregistrering som identifierar ett visst program och tillhörande konfigurationer. Det här[program-ID:t (klient-ID)](https://tools.ietf.org/html/rfc6749#page-15)används när autentiseringsbegäranden utförs och tillhandahålls till autentiseringsbiblioteken under utvecklingstiden. Program-ID (klient-ID) är inte en hemlighet.

## <a name="application-manifest"></a>ansökan manifest

En funktion som tillhandahålls av [Azure-portalen][AZURE-portal], som producerar en JSON-representation av programmets identitetskonfiguration, som används som en mekanism för att uppdatera tillhörande [Application][Graph-App-Resource] och ServicePrincipal-entiteter. [ServicePrincipal][Graph-Sp-Resource] Mer information [finns i Förstå Azure Active Directory-programmanifestet.][AAD-App-Manifest]

## <a name="application-object"></a>programobjekt

När du registrerar/uppdaterar ett program i [Azure-portalen][AZURE-portal]skapar/uppdaterar portalen både ett programobjekt och ett motsvarande [huvudobjekt](#service-principal-object) för tjänsten för den klienten. Programobjektet *definierar* programmets identitetskonfiguration globalt (över alla klienter där det har åtkomst) som tillhandahåller en mall som motsvarande tjänsthuvudobjekt(er) *härleds* för användning lokalt vid körning (i en viss klient).

Mer information finns i [Program- och tjänsthuvudobjekt][AAD-App-SP-Objects].

## <a name="application-registration"></a>programregistrering

För att ett program ska kunna integreras med och delegera identitets- och åtkomsthanteringsfunktioner till Azure AD måste det registreras hos en Azure [AD-klientorganisation](#tenant). När du registrerar ditt program med Azure AD tillhandahåller du en identitetskonfiguration för ditt program, så att det kan integreras med Azure AD och använda funktioner som:

* Robust hantering av enkel inloggning med Azure AD Identity Management och [OpenID][OpenIDConnect] Connect-protokollimplementering
* Förmedlad åtkomst till [skyddade resurser](#resource-server) av [klientprogram](#client-application), via OAuth 2.0 [auktoriseringsserver](#authorization-server)
* [Medgivande ram för](#consent) hantering av klientåtkomst till skyddade resurser, baserat på resursägare auktorisering.

Mer information [finns i Integrera program med Azure Active Directory.][AAD-Integrating-Apps]

## <a name="authentication"></a>autentisering

Handlingen att utmana en part för legitima referenser, vilket utgör grunden för skapandet av ett säkerhetsobjekt som ska användas för identitet och åtkomstkontroll. Under ett [OAuth2-auktoriseringsbidrag](#authorization-grant) till exempel fyller partens autentisering rollen för antingen [resursägare](#resource-owner) eller [klientprogram](#client-application), beroende på vilket bidrag som används.

## <a name="authorization"></a>auktorisering

Handlingen att bevilja en autentiserat säkerhetstillstånd tillstånd att göra något. Det vinns två huvudsakliga typer i Azure AD-programmeringsmodellen:

* Under ett [OAuth2-auktoriseringsbidragsflöde:](#authorization-grant) när [resursägaren](#resource-owner) beviljar auktorisering till [klientprogrammet](#client-application)så att klienten kan komma åt resursägarens resurser.
* Under resursåtkomst av klienten: som implementerats av [resursservern](#resource-server), med hjälp av [anspråksvärden](#claim) som finns i [åtkomsttoken](#access-token) för att fatta beslut om åtkomstkontroll baserat på dem.

## <a name="authorization-code"></a>auktoriseringskod

En kortlivad "token" som tillhandahålls ett [klientprogram](#client-application) av [auktoriseringsslutpunkten](#authorization-endpoint), som en del av "auktoriseringskoden" flödet, en av de fyra OAuth2-auktoriseringsbidragen . [authorization grants](#authorization-grant) Koden returneras till klientprogrammet som svar på autentisering av en [resursägare,](#resource-owner)vilket anger att resursägaren har delegerat auktoriseringen för att komma åt de begärda resurserna. Som en del av flödet löses koden senare in för en [åtkomsttoken](#access-token).

## <a name="authorization-endpoint"></a>slutpunkt för auktorisering

En av de slutpunkter som implementerats av [auktoriseringsservern](#authorization-server), som används för att interagera med [resursägaren](#resource-owner) för att tillhandahålla ett [auktoriseringsbidrag](#authorization-grant) under ett OAuth2-auktoriseringsbidragsflöde. Beroende på vilket auktoriseringsbidragsflöde som används kan det faktiska bidraget variera, inklusive en [auktoriseringskod](#authorization-code) eller [säkerhetstoken](#security-token).

Mer information finns i OAuth2-specifikationens [behörighetsbidragstyper][OAuth2-AuthZ-Grant-Types] och [auktoriseringsslutpunktsavsnitt][OAuth2-AuthZ-Endpoint] och [OpenIDConnect-specifikationen.][OpenIDConnect-AuthZ-Endpoint]

## <a name="authorization-grant"></a>tillståndsbidrag

En autentiseringsinformation som representerar [resursägarens](#resource-owner) [auktorisering](#authorization) för åtkomst till skyddade resurser, som beviljats ett [klientprogram](#client-application). Ett klientprogram kan använda en av de [fyra bidragstyperna som definieras av OAuth2-auktoriseringsramen][OAuth2-AuthZ-Grant-Types] för att erhålla ett bidrag, beroende på klienttyp/krav: "auktoriseringskodsbidrag", "klientautentiseringsbidrag", "implicit beviljande" och "tilldelning av resursägarelösenordsautentiseringsuppgifter". Autentiseringsuppgifterna som returneras till klienten är antingen en [åtkomsttoken](#access-token)eller en [auktoriseringskod](#authorization-code) (som senare utbyts mot en åtkomsttoken), beroende på vilken typ av auktoriseringsbidrag som används.

## <a name="authorization-server"></a>auktoriseringsserver

Som definieras av [OAuth2 Authorization Framework][OAuth2-Role-Def], den server som ansvarar för att utfärda åtkomsttoken till [klienten](#client-application) efter att ha autentiserat [resursägaren](#resource-owner) och hämtat dess auktorisering. Ett [klientprogram](#client-application) interagerar med auktoriseringsservern vid körning via dess [auktoriserings-](#authorization-endpoint) och [tokenslutpunkter,](#token-endpoint) i enlighet med de OAuth2-definierade [auktoriseringsbidragen](#authorization-grant).

När det gäller integrering av Microsoft Identity Platform-program implementerar Microsoft identity-plattformen rollen för Azure AD-program och Microsoft-tjänst-API:er, till exempel [Microsoft Graph API:er][Microsoft-Graph].

## <a name="claim"></a>Hävdar

En [säkerhetstoken](#security-token) innehåller anspråk som innehåller påståenden om en entitet (till exempel ett [klientprogram](#client-application) eller [resursägare)](#resource-owner)till en annan entitet (till exempel [resursservern](#resource-server)). Anspråk är namn-/värdepar som vidarebefordrar fakta om tokenämnet (till exempel säkerhetsobjektet som autentiserades av [auktoriseringsservern).](#authorization-server) Anspråken som finns i en viss token är beroende av flera variabler, inklusive typ av token, vilken typ av autentiseringsuppgifter som används för att autentisera ämnet, programkonfigurationen osv.

Mer information finns i [Microsoft identity platform token reference.][AAD-Tokens-Claims]

## <a name="client-application"></a>klientprogram

Enligt definitionen i [OAuth2 Authorization Framework][OAuth2-Role-Def], ett program som gör skyddade resursbegäranden på uppdrag av [resursägaren](#resource-owner). Termen "klient" innebär inte några särskilda maskinvaruimplementeringsegenskaper (till exempel om programmet körs på en server, ett skrivbord eller andra enheter).

Ett klientprogram begär [auktorisering](#authorization) från en resursägare för att delta i ett [OAuth2-auktoriseringsbidragsflöde](#authorization-grant) och kan komma åt API:er/data för resursägarens räkning. OAuth2-auktoriseringsramverket [definierar två typer av klienter][OAuth2-Client-Types], "konfidentiella" och "offentliga", baserat på klientens förmåga att upprätthålla sekretessen för sina autentiseringsuppgifter. Program kan implementera en [webbklient (konfidentiell)](#web-client) som körs på en webbserver, en [inbyggd klient (offentlig)](#native-client) installerad på en enhet eller en [användaragentbaserad klient (offentlig)](#user-agent-based-client) som körs i en enhets webbläsare.

## <a name="consent"></a>Samtycke

Processen för en [resursägare](#resource-owner) som beviljar auktorisering till ett [klientprogram](#client-application), för att komma åt skyddade resurser under specifika [behörigheter](#permissions), för resursägarens räkning. Beroende på de behörigheter som begärs av klienten kommer en administratör eller användare att bli ombedd att ge samtycke för att ge åtkomst till deras organisation/enskilda data respektive. I ett scenario [med flera innehavare](#multi-tenant-application) registreras programmets [tjänsthuvudnamn](#service-principal-object) också i klienten för den medgivande användaren.

Se [samtyckesramverket](consent-framework.md) för mer information.

## <a name="id-token"></a>ID-token

En [OpenID][OpenIDConnect-ID-Token] [Connect-säkerhetstoken](#security-token) som tillhandahålls av en [auktoriseringsservers](#authorization-server) [auktoriseringsslutpunkt](#authorization-endpoint), som innehåller [anspråk](#claim) som hör till autentisering av en ägare av en [slutanvändarresurs](#resource-owner). Precis som en åtkomsttoken representeras ID-token också som en digitalt signerad [JSON Web Token (JWT)][JWT]. Till skillnad från en åtkomsttoken används dock inte en ID-tokens anspråk för resursåtkomst och specifikt åtkomstkontroll.

Mer information finns i [Microsoft identity platform token reference.][AAD-Tokens-Claims]

## <a name="microsoft-identity-platform"></a>Microsoft-identitetsplattform

Microsoft Identity-plattformen är en utveckling av identitetstjänsten och utvecklingsplattformen Azure Active Directory (Azure AD). Den hjälper utvecklare att bygga program som loggar in alla Microsoft-identiteter, får tokens för att anropa Microsoft Graph, andra Microsoft API:er eller API:er som utvecklare har byggt. Det är en fullfjädrad plattform som består av en autentiseringstjänst, bibliotek, programregistrering och konfiguration, fullständig utvecklardokumentation, kodexempel och annat utvecklarinnehåll. Microsoft Identity-plattformen stöder branschstandardprotokoll som OAuth 2.0 och OpenID Connect. Mer information finns i [Om Microsofts identitetsplattform.](about-microsoft-identity-platform.md)

## <a name="multi-tenant-application"></a>program för flera innehavare

En klass av program som gör det möjligt att logga in och [medgivande](#consent) av användare som etablerats i en Azure [AD-klientorganisation](#tenant), inklusive andra klienter än den där klienten är registrerad. [Inbyggda klientprogram](#native-client) är flera innehavare som standard, medan [webbklient-](#web-client) och [webbresurs-/API-program](#resource-server) har möjlighet att välja mellan enstaka eller flera innehavare. Däremot skulle ett webbprogram som registrerats som en enda klient, endast tillåta inloggningar från användarkonton som etablerats i samma klient som den där programmet är registrerat.

Mer information [finns i Så här loggar du in alla Azure AD-användare med hjälp][AAD-Multi-Tenant-Overview] av programmönstret för flera innehavare.

## <a name="native-client"></a>inbyggd klient

En typ av [klientprogram](#client-application) som installeras internt på en enhet. Eftersom all kod körs på en enhet betraktas den som en "offentlig" klient på grund av dess oförmåga att lagra autentiseringsuppgifter privat/konfidentiellt. Mer information finns i [OAuth2-klienttyper och profiler.][OAuth2-Client-Types]

## <a name="permissions"></a>Behörigheter

Ett [klientprogram](#client-application) får åtkomst till en [resursserver](#resource-server) genom att deklarera behörighetsbegäranden. Det finns två typer:

* "Delegerade" behörigheter, som anger [scopebaserad](#scopes) åtkomst med delegerad auktorisering från den inloggade [resursägaren,](#resource-owner)presenteras för resursen vid körning som ["scp"-anspråk](#claim) i klientens [åtkomsttoken](#access-token).
* "Program"-behörigheter, som anger [rollbaserad](#roles) åtkomst med klientprogrammets autentiseringsuppgifter/identitet, visas för resursen vid körning som ["roller"-anspråk](#claim) i klientens åtkomsttoken.

De visas också under [medgivandeprocessen,](#consent) vilket ger administratören eller resursägaren möjlighet att bevilja/neka klientåtkomst till resurser i sin klientorganisation.

Behörighetsbegäranden konfigureras på **API-behörighetssidan** för ett program i [Azure-portalen][AZURE-portal], genom att välja önskade "Delegerade behörigheter" och "Programbehörigheter" (den senare kräver medlemskap i rollen Global administratör). Eftersom en [offentlig klient](#client-application) inte kan underhålla autentiseringsuppgifter på ett säkert sätt kan den bara begära delegerade behörigheter, medan en [konfidentiell klient](#client-application) har möjlighet att begära både delegerade och programbehörigheter. Klientens [programobjekt](#application-object) lagrar de deklarerade behörigheterna i [egenskapen requiredResourceAccess][Graph-App-Resource].

## <a name="resource-owner"></a>resursägare

Enligt definitionen i [OAuth2 Authorization Framework][OAuth2-Role-Def], en enhet som kan bevilja åtkomst till en skyddad resurs. När resursägaren är en person kallas den för slutanvändare. Till exempel när ett [klientprogram](#client-application) vill komma åt en användares postlåda via [Microsoft Graph API,][Microsoft-Graph]kräver det tillstånd från resursägaren av postlådan.

## <a name="resource-server"></a>resursserver

Som definieras av [OAuth2 Authorization Framework][OAuth2-Role-Def], en server som är värd för skyddade resurser, som kan acceptera och svara på skyddade resursbegäranden av [klientprogram](#client-application) som presenterar en [åtkomsttoken](#access-token). Kallas även en skyddad resursserver eller resursprogram.

En resursserver exponerar API:er och tillämpar åtkomst till sina skyddade resurser via [scope](#scopes) och [roller](#roles)med hjälp av Auktoriseringsramverket OAuth 2.0. Exempel på detta är [Microsoft Graph API][Microsoft-Graph] som ger åtkomst till Azure AD-klientdata och Api:erna för Office 365 som ger åtkomst till data som e-post och kalender. 

Precis som ett klientprogram upprättas resursprogrammets identitetskonfiguration via [registrering](#application-registration) i en Azure AD-klientorganisation, vilket ger både programmet och tjänstens huvudobjekt. Vissa API:er som tillhandahålls av Microsoft, till exempel Microsoft Graph API, har förregistrerade tjänsthuvudnamn som gjorts tillgängliga i alla klienter under etableringen.

## <a name="roles"></a>roles

Precis [som scope](#scopes)är roller ett sätt för en [resursserver](#resource-server) att styra åtkomsten till sina skyddade resurser. Det finns två typer: en "användare"-roll implementerar rollbaserad åtkomstkontroll för användare/grupper som kräver åtkomst till resursen, medan en "program"-roll implementerar samma för [klientprogram](#client-application) som kräver åtkomst.

Roller är resursdefinierade strängar (till exempel "Utgiftsgodkännare", "Skrivskyddad", "Directory.ReadWrite.All"), som hanteras i [Azure-portalen][AZURE-portal] via resursens [programmanifest](#application-manifest)och lagras i [resursens appRoles-egenskap][Graph-Sp-Resource]. Azure-portalen används också för att tilldela användare till "användarroller" och konfigurera [klientprogrambehörigheter](#permissions) för att komma åt en "program"-roll.

En detaljerad diskussion om de programroller som exponeras av Microsoft Graph API finns i [Behörighetsområden för diagrambehörighet][Graph-Perm-Scopes]. Ett steg-för-steg-implementeringsexempel finns i [Hantera åtkomst med RBAC och Azure-portalen][AAD-RBAC].

## <a name="scopes"></a>scopes

Precis som [roller](#roles)är scope ett sätt för en [resursserver](#resource-server) att styra åtkomsten till sina skyddade resurser. Scope används för att implementera [scopebaserad][OAuth2-Access-Token-Scopes] åtkomstkontroll för ett [klientprogram](#client-application) som har getts delegerad åtkomst till resursen av ägaren.

Scope är resursdefinierade strängar (till exempel "Mail.Read", "Directory.ReadWrite.All"), som hanteras i [Azure-portalen][AZURE-portal] via resursens [programmanifest](#application-manifest)och lagras i resursens [egenskap oauth2Permissions][Graph-Sp-Resource]. Azure-portalen används också för att konfigurera [delegerade behörigheter](#permissions) för klientprogram för att komma åt ett scope.

En namngivningskonvention för bästa praxis är att använda formatet "resource.operation.constraint". En detaljerad diskussion om de scope som exponeras av Microsoft Graph API finns i [Behörighetsområden för diagrambehörighet][Graph-Perm-Scopes]. Information om scope som exponeras av Office 365-tjänster finns i [office 365 API-behörigheter referens][O365-Perm-Ref].

## <a name="security-token"></a>säkerhetstoken

Ett signerat dokument som innehåller anspråk, till exempel en OAuth2-token eller SAML 2.0-gällande. För ett [OAuth2-auktoriseringsbeslag](#authorization-grant)är en [åtkomsttoken](#access-token) (OAuth2) och en [ID-token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) typer av säkerhetstoken, som båda implementeras som en [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>huvudobjekt för tjänsten

När du registrerar/uppdaterar ett program i [Azure-portalen][AZURE-portal]skapar/uppdaterar portalen både ett [programobjekt](#application-object) och ett motsvarande huvudobjekt för tjänsten för den klienten. Programobjektet *definierar* programmets identitetskonfiguration globalt (över alla klienter där det associerade programmet har beviljats åtkomst) och är den mall från vilken motsvarande tjänsthuvudobjekt(er) *härleds* för användning lokalt vid körning (i en viss klient).

Mer information finns i [Program- och tjänsthuvudobjekt][AAD-App-SP-Objects].

## <a name="sign-in"></a>sign-in

Processen för ett [klientprogram](#client-application) som initierar slutanvändarens autentisering och fånga relaterat tillstånd, i syfte att hämta en [säkerhetstoken](#security-token) och omfånga programsessionen till det tillståndet. Tillstånd kan innehålla artefakter som användarprofilinformation och information som härleds från tokenanspråk.

Inloggningsfunktionen för ett program används vanligtvis för att implementera enkel inloggning (SSO). Den kan också föregås av en "registreringsfunktion", som startpunkt för en slutanvändare att få tillgång till ett program (vid första inloggningen). Registreringsfunktionen används för att samla in och bevara ytterligare tillstånd som är specifikt för användaren och kan kräva [användarens medgivande](#consent).

## <a name="sign-out"></a>utloggning

Processen att avauktorisera en slutanvändare, koppla bort användartillståndet som är associerat med [klientprogramsessionen](#client-application) under [inloggningen](#sign-in)

## <a name="tenant"></a>tenant

En instans av en Azure AD-katalog kallas en Azure AD-klientorganisation. Det ger flera funktioner, inklusive:

* en registertjänst för integrerade program
* autentisering av användarkonton och registrerade program
* REST-slutpunkter som krävs för att stödja olika protokoll, inklusive OAuth2 och SAML, inklusive [auktoriseringsslutpunkten,](#authorization-endpoint) [tokenslutpunkten](#token-endpoint) och den "vanliga" slutpunkten som används av [program med flera innehavare](#multi-tenant-application).

Azure AD-klienter skapas/associeras med Azure- och Office 365-prenumerationer under registreringen, vilket ger & åtkomsthanteringsfunktioner för prenumerationen. Azure-prenumerationsadministratörer kan också skapa ytterligare Azure AD-klienter via Azure-portalen. Mer information om hur du får en [Azure Active Directory-klient][AAD-How-To-Tenant] för information om olika sätt att få åtkomst till en klientorganisation. Se [Hur Azure-prenumerationer associeras med Azure Active Directory][AAD-How-Subscriptions-Assoc] för information om relationen mellan prenumerationer och en Azure AD-klientorganisation.

## <a name="token-endpoint"></a>tokenslutpunkt

En av de slutpunkter som implementerats av [auktoriseringsservern](#authorization-server) för att stödja OAuth2-auktoriseringsbidrag . [authorization grants](#authorization-grant) Beroende på bidraget kan den användas för att hämta en [åtkomsttoken](#access-token) (och relaterad "uppdatera"-token) till en [klient](#client-application)eller [ID-token](#id-token) när den används med [OpenID Connect-protokollet.][OpenIDConnect]

## <a name="user-agent-based-client"></a>Användaragentbaserad klient

En typ av [klientprogram](#client-application) som hämtar kod från en webbserver och körs inom en användaragent (till exempel en webbläsare), till exempel ett ensidigt program (SPA). Eftersom all kod körs på en enhet betraktas den som en "offentlig" klient på grund av dess oförmåga att lagra autentiseringsuppgifter privat/konfidentiellt. Mer information finns i [OAuth2-klienttyper och profiler][OAuth2-Client-Types].

## <a name="user-principal"></a>användarens huvudnamn

I likhet med hur ett huvudobjekt för tjänsten används för att representera en programinstans är ett användarobjekt en annan typ av säkerhetsobjekt som representerar en användare. [Microsoft Graph-användarresurstypen][Graph-User-Resource] definierar schemat för ett användarobjekt, inklusive användarrelaterade egenskaper som för- och efternamn, användarnamn, katalogrollmedlemskap osv. Detta ger användaridentitetskonfigurationen för Azure AD för att upprätta ett användarobjekt vid körning. Användarens huvudnamn används för att representera en autentiserat användare för enkel inloggning, registrera [samtyckesdelegering,](#consent) fatta beslut om åtkomstkontroll osv.

## <a name="web-client"></a>webbklient

En typ av [klientprogram](#client-application) som kör all kod på en webbserver och som kan fungera som en "konfidentiell" klient genom att lagra autentiseringsuppgifterna på servern på ett säkert sätt. Mer information finns i [OAuth2-klienttyper och profiler][OAuth2-Client-Types].

## <a name="next-steps"></a>Nästa steg

[Microsoft identity platform Developer's Guide][AAD-Dev-Guide] är den målsida som ska användas för alla utvecklingsrelaterade ämnen för Microsoft identity platform, inklusive en översikt över [programintegrering][AAD-How-To-Integrate] och grunderna i [Microsofts identitetsplattformsautentisering och autentiseringsscenarier som stöds][AAD-Auth-Scenarios]. Du kan också hitta kodexempel & självstudier om hur du kommer igång snabbt på [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Använd följande kommentarsavsnitt för att ge feedback och hjälp att förfina och forma det här innehållet, inklusive begäranden om nya definitioner eller uppdatering av befintliga!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal?view=graph-rest-beta
[Graph-User-Resource]: /graph/api/resources/user
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
