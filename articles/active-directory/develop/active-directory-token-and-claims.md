---
title: Lär dig mer om olika token och anspråkstyper som stöds av Azure AD | Microsoft Docs
description: En guide för att förstå och utvärdera anspråk i SAML 2.0 och token JWT (JSON Web) token som utfärdas av Azure Active Directory (AAD)
documentationcenter: na
author: hpsin
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/07/2017
ms.author: hirsin
ms.custom: aaddev
ms.openlocfilehash: 3d8a4ddd98086252f36eeb7034248e909fec1ac0
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="azure-ad-token-reference"></a>Tokenreferens för Azure AD
Azure Active Directory (AD Azure) genererar flera typer av säkerhetstoken vid bearbetning av varje autentiseringsflödet. Det här dokumentet beskriver format, säkerhet egenskaperna och innehållet i varje typ av token.

## <a name="types-of-tokens"></a>Typer av token
Azure AD stöder den [OAuth 2.0-protokollet för auktorisering](active-directory-protocols-oauth-code.md), som utnyttjar både access_tokens och refresh_tokens.  Det stöder också autentisering och logga in via [OpenID Connect](active-directory-protocols-openid-connect-code.md), som innehåller en tredje typ av id_token-token.  Var och en av dessa token representeras som ett ”ägartoken”.

Ett ägartoken är en förenklad säkerhetstoken som ger ”ägar” åtkomst till en skyddad resurs. På detta sätt är ”innehavaren” någon part som kan ge token. Även om autentisering med Azure AD krävs för att få ett ägartoken, vidtas åtgärder för att skydda token för att förhindra avlyssning av en oavsiktlig part. Eftersom ägar-token inte har en inbyggd metod för att förhindra att obehöriga personer använder dem, transporteras de i en säker kanal, till exempel transport layer security (HTTPS). Om ett ägartoken skickas i klartext, kan en man-i mitten-attack användas för att hämta token och få obehörig åtkomst till en skyddad resurs. Samma säkerhetsprinciper tillämpas när lagring eller cachelagring ägar-token för senare användning. Se alltid till att appen skickar och lagrar ägar-token på ett säkert sätt. Flera säkerheten på ägar-token finns [RFC 6750 avsnitt 5](http://tools.ietf.org/html/rfc6750).

Många av de token som utfärdas av Azure AD implementeras som JSON Web token eller JWTs.  En JWT är ett kompakt, URL-säkert sätt att överföra information mellan två parter.  Informationen i JWTs kallas ”anspråk” eller intyg för information om ägar- och ämnet för token.  Anspråk i JWTs är JSON-objekt kodade och serialiseras för överföring.  Eftersom JWTs som utfärdats av Azure AD är signerat, men inte är krypterade, kan du enkelt kontrollerar du innehållet i en JWT för felsökning.  Det finns flera verktyg för att göra det, t.ex [jwt.ms](https://jwt.ms/). Mer information om JWTs som du kan referera till den [JWT-specifikationen](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens
Id_tokens är en form av inloggning säkerhetstoken som appen tar emot när du utför autentisering med [OpenID Connect](active-directory-protocols-openid-connect-code.md).  De visas i form av [JWTs](#types-of-tokens), och innehåller anspråk som du kan använda för att signera användaren i din app.  Du kan använda anspråken i en id_token enligt önskemål – de används ofta för att visa kontoinformation eller gör besluten om åtkomstkontroll i en app.

Id_tokens är signerat, men inte krypterade just nu.  När appen tar emot en id_token, måste [verifiera signaturen](#validating-tokens) till token för autentisering och validera några anspråk i token för att bevisa sin giltighetsperiod.  De anspråk som verifieras av en app varierar beroende på krav för scenarier, men det finns några [vanliga anspråk verifieringar](#validating-tokens) som din app måste utföra i varje scenario.

Se avsnittet följande information på id_tokens anspråk, samt en exempel-id_token.  Observera att anspråk i id_tokens inte returneras i någon särskild ordning.  Dessutom nya anspråk som kan vara introduceras i id_tokens när som helst i tid - appen bryta inte som introduceras nya anspråk.  Följande lista innehåller krav som din app på ett tillförlitligt sätt kan tolka när detta skrivs.  Om nödvändigt, även mer information finns i den [OpenID Connect specifikationen](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Exempel id_token
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> Försök undersöks anspråk i exemplet id_token genom att klistra in det i övningen [jwt.ms](https://jwt.ms/).
> 
> 

#### <a name="claims-in-idtokens"></a>Anspråk i id_tokens
> [!div class="mx-codeBreakAll"]
| JWT-anspråk | Namn | Beskrivning |
| --- | --- | --- |
| `appid` |Program-ID:t |Identifierar det program som använder token för åtkomst till en resurs. Programmet kan fungera som sig själv eller för en användares räkning. Program-ID representerar vanligen programobjekt, men det kan också vara en UPN serviceobjektet i Azure AD. <br><br> **JWT exempelvärde**: <br> `"appid":"15CB020F-3984-482A-864D-1D92265E8268"` |
| `aud` |Målgrupp |Den avsedda mottagaren av token. Det program som tar emot en token måste kontrollera att värdet för målgruppen är korrekt och ignorera eventuella tokens som är avsedda för en annan publik. <br><br> **Exempel SAML värdet**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **JWT exempelvärde**: <br> `"aud":"https://contoso.com"` |
| `appidacr` |Application Authentication Context Class Reference |Anger hur klienten autentiserades. För en offentlig klient är värdet 0. Om du använder klient-ID och klienthemlighet, är värdet 1. <br><br> **JWT exempelvärde**: <br> `"appidacr": "0"` |
| `acr` |Authentication Context Class Reference |Anger hur ämnet autentiserades klienten i Application Authentication Context Class Reference-anspråk. Värdet ”0” anger slutanvändarens autentisering inte uppfyllde kraven i ISO/IEC 29115. <br><br> **JWT exempelvärde**: <br> `"acr": "0"` |
| Autentisering snabbmeddelanden |Registrerar datum och tid då autentisering inträffade. <br><br> **Exempel SAML värdet**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |Autentiseringsmetod |Anger hur ämnet för token autentiserades. <br><br> **Exempel SAML värdet**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **JWT exempelvärde**: `“amr”: ["pwd"]` |
| `given_name` |Förnamn |Innehåller först eller ”” namnet på användaren som angetts i Azure AD-användarobjektet. <br><br> **Exempel SAML värdet**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **JWT exempelvärde**: <br> `"given_name": "Frank"` |
| `groups` |Grupper |Innehåller objekt-ID som representerar användarens gruppmedlemskap. Dessa värden är unik (se objekt-ID) och kan användas på ett säkert sätt för att hantera åtkomst till exempel framtvinga tillstånd att komma åt en resurs. Grupper som ingår i grupper anspråket konfigureras på en applikationsspecifik basis via egenskapen ”groupMembershipClaims” i programmanifestet. En null-värde kan du inte alla grupper, värdet ”säkerhetsgruppen” kommer Ta endast med Active Directory-säkerhetsgrupp medlemskap och ett värde av ”alla” kommer inkluderar både säkerhetsgrupper och distributionslistor för Office 365. <br><br> **Anteckningar**: <br> Finns det `hasgroups` anspråk nedan för mer information om hur du använder den `groups` anspråk med implicit bevilja.  <br> För andra flöden om antalet grupper som användaren är i går över gränsen (150 för SAML 200 för JWT) och sedan en överförbrukning anspråk läggs anspråk källorna pekar på slutpunkten diagrammet som innehåller listan över grupper för användaren. (i. <br><br> **Exempel SAML värdet**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **JWT exempelvärde**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
|`hasgroups` | JWT implicita flödet grupper överförbrukning indikator| Om den finns alltid `true`, som anger användaren är i minst en grupp.  Används i stället för den `groups` anspråk för JWTs i implicit grant-flöden om fullständig grupper anspråk skulle utöka URI-fragment överskrider gränserna för URL-längd (för närvarande 6 eller flera grupper).  Anger att klienten ska använda i diagrammet för att avgöra användarens grupper (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` <br> `http://schemas.microsoft.com/claims/groups.link` | Grupper överförbrukning indikator | För token-förfrågningar som inte är begränsad längd (se `hasgroups` ovan) men fortfarande är för stor för token för en länk till fullständigt grupper för användaren ska inkluderas.  För JWTs som ett distribuerat anspråk för SAML som ett nytt anspråk i stället för den `groups` anspråk. <br><br> **Exempel SAML värdet**: <br> `<Attribute Name=” http://schemas.microsoft.com/claims/groups.link”>`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` <br><br> **JWT exempelvärde**: <br> `"groups":"src1` <br> `_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }`|
| `idp` |Identitetsprovider |Registrerar den identitetsleverantör som autentiserats föremål för token. Det här värdet är identiskt med utfärdaren anspråkets värde såvida inte användarkontot finns i en annan klient än utfärdaren. <br><br> **Exempel SAML värdet**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **JWT exempelvärde**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |Lagrar den tid då token har utfärdats. Den används ofta för att mäta token dokumentens. <br><br> **Exempel SAML värdet**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **JWT exempelvärde**: <br> `"iat": 1390234181` |
| `iss` |Utgivare |Identifierar den säkerhetstokentjänst (STS) som skapar och returnerar token. I de token som Azure AD returnerar är utfärdaren sts.windows.net. GUID i anspråksvärdet utfärdare är klient-ID i Azure AD-katalogen. Klient-ID är oföränderlig och tillförlitlig identifierare för katalogen. <br><br> **Exempel SAML värdet**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **JWT exempelvärde**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |Efternamn |Innehåller sista namn, efternamn eller efternamn för användaren som har definierats i Azure AD-användarobjektet. <br><br> **Exempel SAML värdet**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **JWT exempelvärde**: <br> `"family_name": "Miller"` |
| `unique_name` |Namn |Ger en mänsklig läsbar värde som identifierar föremål för token. Det här värdet är inte säkert att vara unika inom en klient och är avsedd att användas endast för visning. <br><br> **Exempel SAML värdet**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **JWT exempelvärde**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |Objekt-ID |Innehåller en unik identifierare för ett objekt i Azure AD. Det här värdet är oföränderlig och kan inte tilldela om eller återanvänds. Använd objekt-ID för att identifiera ett objekt i frågor till Azure AD. <br><br> **Exempel SAML värdet**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **JWT exempelvärde**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |Roller |Representerar alla roller för programmet som ämnet har beviljats direkt eller indirekt via gruppmedlemskap och kan användas för att använda rollbaserad åtkomstkontroll. Programroller definieras på en applikationsspecifik basis via den `appRoles` egenskapen för programmanifestet. Den `value` egenskapen för varje roll för programmet är det värde som visas i roller anspråket. <br><br> **Exempel SAML värdet**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **JWT exempelvärde**: <br> `“roles”: ["Admin", … ]` |
| `scp` |Omfång |Anger personifiering behörigheterna för klientprogrammet. Standardbehörigheten är `user_impersonation`. Ägaren till den skyddade resursen kan registrera ytterligare värden i Azure AD. <br><br> **JWT exempelvärde**: <br> `"scp": "user_impersonation"` |
| `sub` |Ämne |Identifierar principal om vilka token Assert information, till exempel användaren av ett program. Det här värdet kan inte ändras och det går inte att tilldela om eller återanvänds, så den kan användas för att utföra auktoriseringskontroller på ett säkert sätt. Eftersom ämnet finns alltid i token Azure AD-problem, rekommenderar vi använder det här värdet i ett system med generella tillstånd. <br> `SubjectConfirmation` är inte ett anspråk. Beskriver hur ämnet för token har verifierats. `Bearer` Anger att ämnet bekräftas av deras tillgång token. <br><br> **Exempel SAML värdet**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **JWT exempelvärde**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |Klient-ID:t |En ändras, icke-återanvändningsbara ID som identifierar directory-klient som utfärdade token. Du kan använda det här värdet till klient-specifika directory resurser i ett program med flera innehavare. Du kan till exempel använda det här värdet för att identifiera klienten i ett anrop till Graph API. <br><br> **Exempel SAML värdet**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **JWT exempelvärde**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`, `exp` |Livslängd för token |Definierar det tidsintervall inom vilket en token är giltig. Den tjänst som validerar token bör kontrollera att det aktuella datumet ligger inom livslängd för token, annars avvisar den token. Tjänsten kan tillåta för upp till fem minuter överskrida livslängd för token att beakta eventuella skillnader i tiden (”tiden förskjutning”) mellan Azure AD och tjänsten. <br><br> **Exempel SAML värdet**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **JWT exempelvärde**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |Användarens huvudnamn |Lagrar namnet på användarens huvudnamn.<br><br> **JWT exempelvärde**: <br> `"upn": frankm@contoso.com` |
| `ver` |Version |Lagrar versionsnumret för token. <br><br> **JWT exempelvärde**: <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>Åtkomst-token
När autentiseringen lyckas returnerar Azure AD åtkomst-token som kan användas för att komma åt skyddade resurser. Åtkomsttoken är en base 64-kodat JSON-Webbtoken (JWT) och dess innehåll kan kontrolleras genom att köra det via en avkodare.

Om din app bara *använder* åtkomsttoken att få åtkomst till API: er, du kan (och bör) ska behandlas åtkomsttoken som helt ogenomskinlig – de är bara strängar som din app kan skicka till resurser i HTTP-begäranden.

När du begär en åtkomst-token returnerar Azure AD även vissa metadata om åtkomsttoken för förbrukning av din app.  Informationen omfattar förfallotiden för åtkomst-token och scope som är giltig.  På så sätt kan din app att utföra intelligent cachelagring av åtkomsttoken utan att behöva öppna att parsa den åtkomst-token.

Om din app är en API som skyddas med Azure AD som förväntar sig åtkomst-token i HTTP-begäranden, bör sedan du utföra verifiering och kontroll av token som du får. Din app ska utföra verifieringen av den åtkomst-token innan du använder den för att komma åt resurser. Mer information om verifiering finns [verifierar token](#validating-tokens).  
Mer information om hur du gör detta med .NET finns [skydda ett webb-API med hjälp av ägar-token från Azure AD](active-directory-devquickstarts-webapi-dotnet.md).

## <a name="refresh-tokens"></a>Uppdatera token

Uppdatera token är säkerhetstokens som din app använder för att hämta nya åtkomsttoken i en OAuth 2.0-flödet.  Den kan din app att uppnå långsiktiga åtkomst till resurser för en användares räkning utan interaktion med användaren.

Uppdaterings-tokens är flera resurs.  Det vill säga lösas att en uppdateringstoken som togs emot under en Tokenbegäran för en resurs kan för åtkomst-token till en helt annan resurs. Det gör du genom att ange den `resource` parameter i begäran till den aktuella resursen.

Uppdateringstoken är helt ogenomskinlig till din app. De är långlivade, men din app inte att skriva kan förvänta sig att en uppdateringstoken ska gälla för en viss tidsperiod.  Uppdaterings-tokens kan vara inaktuella när som helst för en mängd olika skäl - finns [Token återkallade](#token-revocation) därmed.  Det enda sättet för din app att veta om en uppdateringstoken är giltig är att försöka lösa genom att göra en tokenbegäran till Azure AD-token för slutpunkt.

När du lösa in en uppdateringstoken för en ny åtkomsttoken får du en ny uppdateringstoken i token svaret.  Du bör spara den nyligen utfärdade uppdateringstoken ersätter den du används i begäran.  Detta garanterar att uppdaterings-tokens vara giltigt så länge som möjligt.

## <a name="validating-tokens"></a>Verifiera token

För att kunna verifiera ett id_token eller en access_token, bör du verifiera både signatur token och anspråk din app. För att kunna verifiera åtkomsttoken bör din app också verifiera utfärdaren, målgruppen och signera token. Dessa måste verifieras mot värdena i OpenID discovery-dokumentet. Till exempel klient oberoende versionen av dokumentet finns på [ https://login.microsoftonline.com/common/.well-known/openid-configuration ](https://login.microsoftonline.com/common/.well-known/openid-configuration). Azure AD-mellanprogram har inbyggda funktioner för att validera åtkomst-token och du kan bläddra igenom våra [exempel](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) i önskat språk. Mer information om hur du uttryckligen validerar en JWT-token, finns det [manuell JWT validering exempel](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation).  

Vi tillhandahåller bibliotek och kodexempel som visar hur du kan hantera token validering - den informationen nedan finns bara för användare som vill förstå underliggande processen.  Det finns också flera bibliotek med öppen källkod från tredje part för JWT-verifiering: det finns minst ett alternativ för nästan alla plattform och det språket. Mer information om Azure AD-autentiseringsbibliotek och kodexempel finns [Azure AD-autentiseringsbibliotek](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Verifiera signaturen

En JWT innehåller tre segment som avgränsas med den `.` tecken.  Det första segmentet kallas den **huvud**, andra som den **brödtext**, och tredje som den **signatur**.  Signaturen segment kan användas för att kontrollera äkthet token så att den kan vara betrott av din app.

Token som utfärdats av Azure AD är signerade med industry standard asymmetriska krypteringsalgoritmer, till exempel RSA-256. Rubriken för JWT innehåller information om metoden nyckel och kryptering används för att signera token:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "kriMPdmBvx68skT8-mPAB3BseeA"
}
```

Den `alg` anspråk anger algoritmen som används för att signera token, medan den `x5t` anspråk anger särskild offentlig nyckel som användes för att signera token.

Vid en viss tidpunkt, kan Azure AD logga en id_token med någon av en viss uppsättning privat-offentligt nyckelpar. Azure AD roterar möjliga uppsättning nycklar regelbundet, så att din app ska skrivas till att hantera dessa viktiga ändringar automatiskt.  En rimlig frekvens för att söka efter uppdateringar för offentliga nycklar som används av Azure AD är 24 timmar.

Du kan skaffa signering viktiga data som krävs för att verifiera signaturen med OpenID Connect Metadatadokumentet finns på:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Denna URL i en webbläsare kan du prova!
> 
> 

Metadatadokumentet är en JSON-objekt som innehåller flera användbara delar av information, t.ex platsen för de olika slutpunkter som krävs för att utföra autentisering med OpenID Connect.  

Den innehåller också en `jwks_uri`, vilket ger platsen för en uppsättning offentliga nycklar som används för att signera token.  JSON-dokumentet finns på den `jwks_uri` innehåller all informationen om offentliga nycklar används av den viss tidpunkten.  Din app kan använda den `kid` anspråk i JWT-huvudet för att välja vilka offentliga nyckeln i det här dokumentet har använts för att logga en viss token.  Den kan utföra signaturverifiering med rätt offentlig nyckel och den angivna algoritmen.

Utför signaturverifiering ligger utanför omfånget för det här dokumentet - det finns många bibliotek med öppen källkod för att hjälpa dig att göra det om det behövs.

#### <a name="validating-the-claims"></a>Verifiera anspråk

När appen tar emot en token (en id_token när användaren loggar in, eller en åtkomst-token som en ägartoken i HTTP-begäran) bör det också utföra några kontroller mot anspråk i token.  Dessa inkludera, men är inte begränsade till:

* Den **målgruppen** anspråk - att verifiera att token är avsedd för att tilldelas till din app.
* Den **inte före** och **förfallotid** anspråk - att verifiera att token inte har gått ut.
* Den **utfärdaren** påstår - verifiera att token verkligen har utfärdats till din app av Azure AD.
* Den **temporärt ID** - om du vill minimera en token replay-attacker.
* med mera...

En fullständig lista över anspråk verifieringar din app ska utföra för ID-token som avser den [OpenID Connect specifikationen](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation). Information om de förväntade värdena för dessa anspråk som ingår i den föregående [id_token](#id-tokens) avsnitt.

## <a name="token-revocation"></a>Token återkallade certifikat

Uppdatera token kan ogiltiga eller återkallas när som helst för en mängd olika skäl.  Dessa är indelade i två huvudkategorier: timeout och återkallelse. 
* Tidsgränser för token
  * MaxInactiveTime: Om uppdateringstoken som inte har använts under den tiden enligt MaxInactiveTime uppdatera Token kommer inte längre giltig. 
  * MaxSessionAge: Om MaxAgeSessionMultiFactor eller MaxAgeSessionSingleFactor har angetts till något annat än standard (förrän har återkallats), sedan återautentiseringen måste utföras efter den tid som anges i MaxAgeSession *.  
  * Exempel:
    * Klienten har en MaxInactiveTime på fem dagar och användaren gick på semester för en vecka så AAD har inte visas en ny token begäran från användaren i 7 dagar.  Nästa gång användaren begär en ny token hittar de sina uppdatera Token har återkallats och de måste ange sina autentiseringsuppgifter igen. 
    * En känslig applikation har en MaxAgeSessionSingleFactor på 1 dag.  Om en användare loggar in på måndag och tisdagen (efter 25 timmar har förflutit) uppmanas de att autentisera igen.  
* Återkallade certifikat
  * Frivillig lösenordsändring: Om en användare ändrar sina lösenord, de kanske återautentisera över några av sina program, beroende på hur token har uppnåtts.  Se informationen nedan för undantag. 
  * Ofrivilliga lösenordsändring: Om en administratör tvingar en användare att ändra sina lösenord eller återställer den, sedan användarens token blir ogiltiga om de har uppnås med hjälp av sina lösenord.  Se informationen nedan för undantag. 
  * Säkerhetsintrång: Vid ett intrång (t.ex. det lokala arkivet för lösenord komprometteras) administratören kan återkalla alla uppdaterings-tokens som utfärdats för närvarande.  Detta kräver att alla användare att autentisera igen. 

Obs! 

Om en icke-password-metod för autentisering för (Windows Hello, autentiseringsapp, biometrik som en yta eller fingeravtryck) att uppnå token, ändra användarens lösenord kommer inte att tvinga användaren att autentisera igen (men det tvingar sina autentiseringsapp återautentisera).  Detta beror på att ange sina valda autentisering (en min, t.ex.) inte har ändrats och därför kan användas igen återautentisera.

## <a name="sample-tokens"></a>Exempel-token

Det här avsnittet visas exempel på SAML- och JWT-token som returnerar Azure AD. Dessa exempel kan du se anspråk i kontexten.

### <a name="saml-token"></a>SAML-Token

Det här är ett exempel på en typisk SAML-token.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="http://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="http://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="http://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="http://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

### <a name="jwt-token---user-impersonation"></a>JWT-Token - personifiering
Det här är ett exempel på en typisk JSON web token (JWT) används i en grant-auktoriseringskodflödet.
Förutom anspråk, token innehåller ett versionsnummer i **ver** och **appidacr**, klassreferens kontext för autentisering som anger hur klienten autentiserades. För en offentlig klient är värdet 0. Om en klient-ID eller klienthemlighet användes är värdet 1.

    {
     typ: "JWT",
     alg: "RS256",
     x5t: "kriMPdmBvx68skT8-mPAB3BseeA"
    }.
    {
     aud: "https://contoso.onmicrosoft.com/scratchservice",
     iss: "https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/",
     iat: 1416968588,
     nbf: 1416968588,
     exp: 1416972488,
     ver: "1.0",
     tid: "b9411234-09af-49c2-b0c3-653adc1f376e",
     amr: [
      "pwd"
     ],
     roles: [
      "Admin"
     ],
     oid: "6526e123-0ff9-4fec-ae64-a8d5a77cf287",
     upn: "sample.user@contoso.onmicrosoft.com",
     unique_name: "sample.user@contoso.onmicrosoft.com",
     sub: "yf8C5e_VRkR1egGxJSDt5_olDFay6L5ilBA81hZhQEI",
     family_name: "User",
     given_name: "Sample",
     groups: [
      "0e129f6b-6b0a-4944-982d-f776000632af",
      "323b13b3-1851-4b94-947f-9a4dacb595f4",
      "6e32c250-9b0a-4491-b429-6c60d2ca9a42",
      "f3a161a7-9a58-4e8f-9d47-b70022a07424",
      "8d4c81b2-b1ad-476d-9574-544d155aa6ff",
      "1bf80164-ff24-4866-b19c-6212e5b9a847",
      "76f80127-f2cd-46f4-8c52-8edd8bc749b1",
      "0ba27160-44d0-42b5-b90c-47b3fcc48e35"
     ],
     appid: "b075ddef-0efa-123b-997b-de1337c29185",
     appidacr: "1",
     scp: "user_impersonation",
     acr: "1"
    }.

## <a name="related-content"></a>Relaterat innehåll
* Se Azure AD Graph [åtgärder](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) och [princip entiteten](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity), om du vill veta mer om hur du hanterar livslängd för token-principer via Azure AD Graph API.
* Mer information och exempel på att hantera principer via PowerShell-cmdletar, inklusive exempel, finns [konfigurerbara token livslängd i Azure AD](../active-directory-configurable-token-lifetimes.md). 
* Lägg till [anpassade och valfria anspråk](active-directory-optional-claims.md) till token för ditt program. 
