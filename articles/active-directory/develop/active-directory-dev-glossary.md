---
title: Azure Active Directory developer ordlista | Microsoft Docs
description: En lista över villkor för vanliga begrepp för utvecklare av Azure Active Directory och funktioner.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/16/2017
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: ab053e9b132630c19b6966286035d38c71c6b4d9
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36268138"
---
# <a name="azure-active-directory-developer-glossary"></a>Azure Active Directory developer ordlista
Den här artikeln innehåller definitioner för några av de Azure Active Directory (AD) developer grundbegrepp, vilket underlättar vid utbildning om programutveckling för Azure AD.

## <a name="access-token"></a>åtkomsttoken
En typ av [säkerhetstoken](#security-token) utfärdats av en [auktorisering server](#authorization-server), och används av en [klientprogrammet](#client-application) för att komma åt en [skyddade resursservern](#resource-server). Vanligtvis i form av en [JSON-Webbtoken (JWT)][JWT], token innehåller det tillstånd som beviljats till klienten med den [resursägare](#resource-owner), för en begärda åtkomstnivå. Token som innehåller alla tillämpliga [anspråk](#claim) om ämnet, aktivera klientprogram att använda den som en form av autentiseringsuppgifter vid åtkomst till en viss resurs. Detta även eliminerar behovet för resursägare att exponera autentiseringsuppgifter till klienten.

Åtkomsttoken är ibland kallas ”användare + App” eller ”App-Only” beroende på de autentiseringsuppgifter som representeras. Till exempel när ett klientprogram använder den:

* [”Auktoriseringskoden” authorization grant](#authorization-grant), autentiserar användaren först som resursägare, delegera behörighet till klienten att komma åt resursen. Autentiserar klienten efteråt när åtkomsttoken. Token kan ibland kallas mer specifikt en ”användare +” apptoken, eftersom den representerar både användaren som behörighet klientprogrammet och programmet.
* [”Klientens autentiseringsuppgifter” authorization grant](#authorization-grant), klienten ger den enda autentiseringen fungerar utan den resurs-ägare autentisering/auktorisering, så token kan ibland kallas ”App-Only”-token.

Se [Azure AD tokenreferens] [ AAD-Tokens-Claims] för mer information.

## <a name="application-id-client-id"></a>program-id (klient-id)
Unik identifierare Azure AD-problem registreringen av ett program som identifierar ett visst program och de associera konfigurationerna. Program-id ([klient-id](https://tools.ietf.org/html/rfc6749#page-15)) används när autentisering begär och har angetts till autentiseringsbibliotek i tid. Program-id (klient-id) är inte en hemlighet. 

## <a name="application-manifest"></a>Applikationsmanifestet
En funktion som tillhandahålls av den [Azure-portalen][AZURE-portal], som producerar en JSON-representation av programmets identitet konfiguration används som en mekanism för att uppdatera de associerade [programmet] [ AAD-Graph-App-Entity] och [ServicePrincipal] [ AAD-Graph-Sp-Entity] entiteter. Se [förstå Azure Active Directory-programmanifestet] [ AAD-App-Manifest] för mer information.

## <a name="application-object"></a>Programobjektet
När du registrera/uppdatera ett program i den [Azure-portalen][AZURE-portal], portalen skapat/uppdaterat programobjekt och en motsvarande [tjänstens huvudnamn objekt](#service-principal-object) för den klienten. Programobjektet *definierar* programmet användarens identitet configuration globalt (över alla klienter där den har åtkomst), tillhandahåller en mall som dess motsvarande service principal objekt är *härledda* för lokalt under körning (i en viss klient).

Mer information finns i [program och tjänstens huvudnamn objekt][AAD-App-SP-Objects].

## <a name="application-registration"></a>Registrera programmet
För att ett program för att integrera med och delegera identitets- och åtkomsthantering funktioner till Azure AD, måste den vara registrerad med en Azure AD [klient](#tenant). När du registrerar ditt program med Azure AD, om du tillhandahåller en identity-konfiguration för ditt program så att den kan integreras med Azure AD och använda funktioner som:

* Stabil hantering av enkel inloggning med Azure AD Identity Management och [OpenID Connect] [ OpenIDConnect] protokollimplementering
* Asynkrona åtkomst till [skyddade resurser](#resource-server) av [klientprogram](#client-application), via Azure AD OAuth 2.0 [auktorisering server](#authorization-server) implementering
* [Medgivande framework](#consent) för att hantera åtkomst till skyddade resurser, baserat på resursen ägare auktorisering.

Se [integrera program med Azure Active Directory] [ AAD-Integrating-Apps] för mer information.

## <a name="authentication"></a>autentisering
Åtgärden för att utvärdera en part legitima autentiseringsuppgifter ligger till grund för att skapa ett säkerhetsobjekt som ska användas för identitets- och åtkomstkontroll. Under en [OAuth2 authorization grant](#authorization-grant) exempelvis part autentisera fyller rollen antingen [resursägare](#resource-owner) eller [klientprogrammet](#client-application), beroende på bevilja används.

## <a name="authorization"></a>Auktorisering
Att bevilja en autentiserad huvudnamn behörighet att göra något. Det finns två primära användningsområden i Azure AD-programmeringsmodell:

* Under en [OAuth2 authorization grant](#authorization-grant) flöde: när den [resursägare](#resource-owner) ger behörighet att den [klientprogrammet](#client-application), så att klienten att komma åt resurser i resursägare.
* Under resursåtkomst av klienten: som implementeras av den [resursservern](#resource-server)med hjälp av den [anspråk](#claim) värden finns i den [åtkomsttoken](#access-token) att göra besluten om åtkomstkontroll baserat på dem.

## <a name="authorization-code"></a>Auktoriseringskoden
En kort livslängd ”token” som en [klientprogrammet](#client-application) av den [autentiseringsslutpunkt](#authorization-endpoint), som en del av flödet ”Auktoriseringskoden”, en av de fyra OAuth2 [auktorisering ger](#authorization-grant). Kod som returneras till klientprogrammet som svar på autentisering av en [resursägare](#resource-owner), som anger resursägaren har delegerats behörighet att komma åt de begärda resurserna. Som en del av flödet, Inlöst koden senare för en [åtkomsttoken](#access-token).

## <a name="authorization-endpoint"></a>autentiseringsslutpunkt
En av slutpunkterna som implementerats av den [auktorisering server](#authorization-server), som används för att interagera med den [resursägare](#resource-owner) för att ge en [authorization grant](#authorization-grant) under en OAuth2 auktorisering bevilja flödet. Beroende på authorization grant flödet som används, faktiska bevilja som kan variera, inklusive en [Auktoriseringskoden](#authorization-code) eller [säkerhetstoken](#security-token).

Visa OAuth2-specifikation [auktorisering bevilja typer] [ OAuth2-AuthZ-Grant-Types] och [autentiseringsslutpunkt] [ OAuth2-AuthZ-Endpoint] avsnitt och [OpenIDConnect specifikationen] [ OpenIDConnect-AuthZ-Endpoint] för mer information.

## <a name="authorization-grant"></a>bevilja behörighet
En autentiseringsuppgift som representerar den [resursägare](#resource-owner) [auktorisering](#authorization) att komma åt de skyddade resurser som tilldelas en [klientprogrammet](#client-application). Ett klientprogram kan använda något av de [fyra bevilja typer som definieras i OAuth2 auktorisering Framework] [ OAuth2-AuthZ-Grant-Types] att erhålla ett bidrag, beroende på typ/klientkrav: ”authorization kod grant”, ”klientens autentiseringsuppgifter bevilja”, ”implicit bevilja” och ”lösenordsinformation för resurs-ägare bevilja”. Autentiseringsuppgifterna som returneras till klienten är antingen en [åtkomsttoken](#access-token), eller en [Auktoriseringskoden](#authorization-code) (utväxlats senare för en åtkomst-token) beroende på vilken typ av auktorisering bevilja används.

## <a name="authorization-server"></a>auktorisering server
Som definieras av den [OAuth2 auktorisering Framework][OAuth2-Role-Def], server ansvarar för att utfärda åtkomst-token till den [klienten](#client-application) efter autentisering av [resursägare](#resource-owner) och hämta dess tillstånd. En [klientprogrammet](#client-application) samverkar med auktorisering servern vid körning via dess [auktorisering](#authorization-endpoint) och [token](#token-endpoint) slutpunkter i enlighet med OAuth2 definierats [auktorisering ger](#authorization-grant).

När det gäller Azure AD-integrering för programmet, Azure AD implementerar serverrollen auktorisering för Azure AD-program och Microsoft service API: er, till exempel [Microsoft Graph API: er][Microsoft-Graph].

## <a name="claim"></a>anspråk
En [säkerhetstoken](#security-token) innehåller anspråk som innehåller intyg om en enhet (som en [klientprogrammet](#client-application) eller [resursägare](#resource-owner)) till en annan entitet (som den [resursservern](#resource-server)). Anspråk är namn/värde-par som vidarebefordrar fakta om token område (till exempel det säkerhetsobjekt som autentiseras av den [auktorisering server](#authorization-server)). Anspråk finns i en viss token är beroende av flera variabler, inklusive typ av token, vilken typ av autentiseringsuppgift som används för att autentisera ämne, konfiguration, osv.

Se [Azure AD tokenreferens] [ AAD-Tokens-Claims] för mer information.

## <a name="client-application"></a>klientprogram
Som definieras av den [OAuth2 auktorisering Framework][OAuth2-Role-Def], ett program som gör skyddade resursen begäranden på uppdrag av den [resursägare](#resource-owner). Termen ”klient” innebär inte någon särskild maskinvara implementering egenskaper (till exempel om programmet körs på en server, en stationär dator och andra enheter). 

En klient begär [auktorisering](#authorization) från en resursägare att delta i en [OAuth2 authorization grant](#authorization-grant) flöda och kan komma åt API: er/data för resursägare räkning. OAuth2 auktorisering Framework [definierar två typer av klienter][OAuth2-Client-Types]”konfidentiellt” och ”offentliga”, baserat på klientens möjlighet att skydda sina autentiseringsuppgifter. Program kan implementera en [webbklienten (konfidentiell)](#web-client) som körs på en webbserver, en [native client (offentlig)](#native-client) installeras på en enhet eller en [användaren-agent-baserad klient (offentlig)](#user-agent-based-client) som körs i en enhetens webbläsare.

## <a name="consent"></a>Medgivande
Processen för en [resursägare](#resource-owner) auktoriseringsregler till en [klientprogrammet](#client-application), för att komma åt skyddade resurser under specifika [behörigheter](#permissions), för resursägare. Beroende på vilka behörigheter som begärs av klienten att en administratör eller användare ombeds medgivande att tillåta åtkomst till deras organisation/enskild data respektive. Observera att i en [flera innehavare](#multi-tenant-application) scenario, programmets [tjänstens huvudnamn](#service-principal-object) också registreras i klienten för consenting användaren.

## <a name="id-token"></a>ID-token
En [OpenID Connect] [ OpenIDConnect-ID-Token] [säkerhetstoken](#security-token) som tillhandahålls av en [auktorisering server](#authorization-server) [autentiseringsslutpunkt](#authorization-endpoint), som innehåller [anspråk](#claim) rör verifiering av en slutanvändare [resursägare](#resource-owner). Som ett åtkomsttoken, visas också ID-token som ett digitalt signerat [JSON-Webbtoken (JWT)][JWT]. Till skillnad från en åtkomst-token men anspråk för en ID-token används inte för relaterade till åtkomst till resurser och åtkomstkontroll specifikt.

Se [Azure AD tokenreferens] [ AAD-Tokens-Claims] för mer information.

## <a name="multi-tenant-application"></a>program med flera innehavare
En klass för program som kan logga in och [medgivande](#consent) av användare som har etablerats i Azure AD [klient](#tenant), inklusive klienter än den där klienten har registrerats. [-Klienten](#native-client) program är flera innehavare som standard, medan [webbklienten](#web-client) och [webb-resurs/API](#resource-server) program har möjlighet att välja mellan en eller flera innehavare. Däremot skulle ett webbprogram som är registrerat som en klient tillåter endast inloggningar från användarkonton som har etablerats i samma klientorganisation som den där programmet har registrerats.

Se [loggar in alla Azure AD-användare med flera innehavare programmönster] [ AAD-Multi-Tenant-Overview] för mer information.

## <a name="native-client"></a>-klienten
En typ av [klientprogrammet](#client-application) som internt är installerad på en enhet. Eftersom all kod som körs på en enhet anses en ”offentliga” klient på grund av att det inte att lagra autentiseringsuppgifter privat/konfidentiellt. Se [OAuth2 klienten typer och profiler] [ OAuth2-Client-Types] för mer information.

## <a name="permissions"></a>behörigheter
En [klientprogrammet](#client-application) får åtkomst till en [resursservern](#resource-server) genom att deklarera behörighetsbegäranden. Det finns två typer:

* ”Delegerad” behörigheter, som anger [scope-baserade](#scopes) använda delegerad behörighet från den inloggade [resursägare](#resource-owner), presenteras för resursen vid körning som [”scp” anspråk](#claim) i klientens [åtkomsttoken](#access-token).
* ”Program” behörigheter, som anger [rollbaserad](#roles) åtkomst med hjälp av autentiseringsuppgifter client-program/identitet, presenteras för resursen vid körning som [”roller” anspråk](#claim) i klientens åtkomst-token.

De kan också ansluta under den [medgivande](#consent) processen, vilket ger administratören eller resursägare möjlighet att bevilja/neka klientåtkomst till resurser i klientorganisationen.

Behörighetsbegäranden konfigureras på ”program” / ”inställningar” fliken i den [Azure-portalen][AZURE-portal], under ”nödvändiga behörigheter”, genom att välja önskad ”delegerade behörigheter” och ”programbehörigheter” (det senare kräver medlemskap i rollen Global administratör). Eftersom en [offentliga klienten](#client-application) på ett säkert sätt kan inte upprätthålla autentiseringsuppgifter, den kan bara begära delegerade behörigheter, medan en [konfidentiell klienten](#client-application) har möjlighet att begära både delegerad och program. Klientens [programobjektet](#application-object) lagrar behörigheterna som har deklarerats i dess [requiredResourceAccess egenskapen][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>resursägare
Som definieras av den [OAuth2 auktorisering Framework][OAuth2-Role-Def], en entitet som kan beviljas åtkomst till en skyddad resurs. När resursägaren är en person, kallas det en slutanvändare. Till exempel när en [klientprogrammet](#client-application) vill komma åt en användares postlåda via den [Microsoft Graph API][Microsoft-Graph], krävs behörighet från resursägare av postlådan.

## <a name="resource-server"></a>resursservern
Som definieras av den [OAuth2 auktorisering Framework][OAuth2-Role-Def], en server att värdar skyddade resurser, kan ta emot och svara på skyddade resursen begäranden av [klientprogram](#client-application) som finns ett [åtkomsttoken](#access-token). Kallas även en skyddad resurs-servern eller resursprogram.

Resursservern visar API: er och tillämpar åtkomst till sina skyddade resurser via [scope](#scopes) och [roller](#roles), med hjälp av OAuth 2.0 auktorisering Framework. Exempel: Azure AD Graph-API som ger åtkomst till data i Azure AD-klient och Office 365-API som ger åtkomst till data, till exempel e-post och kalender. Båda dessa är också tillgänglig via den [Microsoft Graph API][Microsoft-Graph]. 

Precis som ett klientprogram, upprättas resurs programmets identitet konfiguration [registrering](#application-registration) tillhandahåller både program och tjänstens huvudnamn objekt i en Azure AD-klient. Vissa Microsoft API: er, till exempel Azure AD Graph-API har redan registrerat tjänstens huvudnamn göras tillgänglig i alla klienter under etableringen.

## <a name="roles"></a>roles
Som [scope](#scopes), roller ge ett sätt för en [resursservern](#resource-server) att styra åtkomst till sina skyddade resurser. Det finns två typer: rollen ”användare” implementerar rollbaserad åtkomstkontroll för användare eller grupper som behöver åtkomst till resursen, medan en ”program” roll implementerar samma för [klientprogram](#client-application) som kräver åtkomst.

Roller är definierad i resursen strängar (till exempel ”utgifter godkännare”, ”skrivskyddad”, ”Directory.ReadWrite.All”), hanteras i den [Azure-portalen] [ AZURE-portal] via resursens [programmanifestet](#application-manifest), och lagras i resursens [appRoles egenskapen][AAD-Graph-Sp-Entity]. Azure-portalen används också för att tilldela användare till ”användare” roller och konfigurera klienten [programbehörigheter](#permissions) åtkomst till en ”program”-roll.

En detaljerad beskrivning av programmet rollerna som exponeras av Azure AD Graph API finns [Behörighetsomfattningen för Graph API][AAD-Graph-Perm-Scopes]. En stegvis implementering exempel finns [hantera åtkomst med hjälp av RBAC och Azure portal][AAD-RBAC].

## <a name="scopes"></a>Scope
Som [roller](#roles), scope ge ett sätt för en [resursservern](#resource-server) att styra åtkomst till sina skyddade resurser. Omfattningar som används för att implementera [scope-baserade] [ OAuth2-Access-Token-Scopes] åtkomstkontroll för en [klientprogrammet](#client-application) som har angetts delegerad åtkomst till resursen av sin ägare.

Scope är definierad i resursen strängar (till exempel ”Mail.Read”, ”Directory.ReadWrite.All”) hanteras i den [Azure-portalen] [ AZURE-portal] via resursens [programmanifestet](#application-manifest), och lagras i resursens [oauth2Permissions egenskapen][AAD-Graph-Sp-Entity]. Azure portal även för att konfigurera klientprogrammet [delegerad behörighet](#permissions) att komma åt ett scope.

En bästa praxis namngivningskonvention, är att använda formatet ”resource.operation.constraint”. En detaljerad beskrivning av de omfattningar som exponeras av Azure AD Graph API finns [Behörighetsomfattningen för Graph API][AAD-Graph-Perm-Scopes]. Scope som exponeras av Office 365-tjänster, se [Office 365-API-referens för behörigheter][O365-Perm-Ref].

## <a name="security-token"></a>säkerhetstoken
Ett signerat dokument som innehåller anspråk, till exempel en OAuth2-token eller SAML 2.0-kontroll. För en OAuth2 [authorization grant](#authorization-grant), en [åtkomsttoken](#access-token) (OAuth2) och en [ID Token](http://openid.net/specs/openid-connect-core-1_0.html#IDToken) typer av säkerhetstoken som implementeras som en [JSON-Webbtoken (JWT)][JWT].

## <a name="service-principal-object"></a>Tjänstens huvudnamn objekt
När du registrera/uppdatera ett program i den [Azure-portalen][AZURE-portal], portalen skapat/uppdaterat både en [programobjektet](#application-object) och ett motsvarande service principal-objekt för den klienten. Programobjektet *definierar* programmets identitet konfiguration globalt (över alla klienter där det associerade programmet har beviljats åtkomst), och är den mall som dess motsvarande service principal objekt är *härledda* för lokalt under körning (i en viss klient).

Mer information finns i [program och tjänstens huvudnamn objekt][AAD-App-SP-Objects].

## <a name="sign-in"></a>inloggning
Processen för en [klientprogrammet](#client-application) initierar slutanvändarens autentisering och samlar in relaterade tillstånd, för att förvärva en [säkerhetstoken](#security-token) och scope program-session till det aktuella tillståndet. Tillstånd kan innehålla artefakter, till exempel användarens profilinformation och information som har härletts från token anspråk.

Funktionen logga in i ett program används vanligtvis för att implementera enkel inloggning (SSO). Det kan också föregås av en funktion som ”registrering” som startpunkt för en användare att få tillgång till ett program (vid första inloggningen). Registrerar funktionen används för att samla in och beständiga ytterligare tillstånd som är specifika för användaren och kan kräva [användargodkännande](#consent).

## <a name="sign-out"></a>utloggning
Processen för unauthenticating slutanvändaren frånkoppling användarens tillstånd som associeras med den [klientprogrammet](#client-application) session under [inloggning](#sign-in)

## <a name="tenant"></a>klient
En instans av en Azure AD-katalog kallas för en Azure AD-klient. Det innehåller flera funktioner, inklusive:

* registry-tjänsten för integrerade program
* autentisering av användarkonton och registrerade program
* REST-slutpunkter som krävs för att stödja olika protokoll, inklusive OAuth2 och SAML, inklusive den [autentiseringsslutpunkt](#authorization-endpoint), [tokenslutpunkten](#token-endpoint) och ”vanliga” slutpunkt som används av [program med flera klienter](#multi-tenant-application).

Azure AD-klienter kan skapas/kopplade till Azure och Office 365-prenumerationer under registreringen, tillhandahåller Identity & Access Management-funktioner för prenumerationen. Azure-prenumerationsadministratörer kan också skapa ytterligare Azure AD-klienter via Azure portal. Se [skaffa en Azure Active Directory-klient] [ AAD-How-To-Tenant] information om de olika sätt kan du få åtkomst till en klient. Se [hur Azure-prenumerationer är associerade med Azure Active Directory] [ AAD-How-Subscriptions-Assoc] information om relationen mellan prenumerationer och Azure AD-klient.

## <a name="token-endpoint"></a>token för slutpunkt
En av slutpunkterna som implementerats av den [auktorisering server](#authorization-server) till support OAuth2 [auktorisering ger](#authorization-grant). Beroende på att bevilja den kan användas för att få en [åtkomsttoken](#access-token) (och relaterade ”uppdatera” token) till en [klienten](#client-application), eller [ID token](#ID-token) när det används med den [OpenID Connect] [ OpenIDConnect] protokoll.

## <a name="user-agent-based-client"></a>Användare-agent-baserad klient
En typ av [klientprogrammet](#client-application) som hämtar koden från en webbserver och kör inom en användaragent (till exempel en webbläsare), till exempel en enda sida program (SPA). Eftersom all kod som körs på en enhet anses en ”offentliga” klient på grund av att det inte att lagra autentiseringsuppgifter privat/konfidentiellt. Mer information finns i [OAuth2 klienten typer och profiler][OAuth2-Client-Types].

## <a name="user-principal"></a>användarens huvudnamn
Ungefär samma sätt som en tjänst för huvudobjekt används för att representera en programinstans, ett huvudnamn användarobjekt är en annan typ av säkerhetsobjekt, som representerar en användare. Azure AD Graph [användarentiteten] [ AAD-Graph-User-Entity] definierar schemat för ett användarobjekt, inklusive användarrelaterade egenskaper och efternamn, användarens huvudnamn, directory rollmedlemskap, t.ex. Detta ger identitet Användarkonfiguration för Azure AD för att upprätta en UPN vid körning. Användarens huvudnamn används för att representera en autentiserad användare för enkel inloggning, registrering [medgivande](#consent) delegering, vilket gör besluten om åtkomstkontroll, osv.

## <a name="web-client"></a>Webbklient
En typ av [klientprogrammet](#client-application) som utför all kod på en webbserver och kan fungera som en ”konfidentiellt” klient genom att lagra sina autentiseringsuppgifter på ett säkert sätt på servern. Mer information finns i [OAuth2 klienten typer och profiler][OAuth2-Client-Types].

## <a name="next-steps"></a>Nästa steg
Den [Azure AD-Guide för utvecklare] [ AAD-Dev-Guide] är Landningssida för alla Azure AD-utveckling-relaterade ämnen, t.ex. en översikt över [programintegrationstyp] [ AAD-How-To-Integrate] och grunderna i [scenarier för autentiseringsmetoder som stöds och Azure AD authentication][AAD-Auth-Scenarios]. Du kan också hitta kodexempel och självstudier om hur du kommer igång snabbt på [Github](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Använd följande avsnitt för kommentarer för att ge feedback och hjälper dig att förfina och utforma innehållet, inklusive förfrågningar efter nya definitioner eller uppdaterar befintliga!

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken
