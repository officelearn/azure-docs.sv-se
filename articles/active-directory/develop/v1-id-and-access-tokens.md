---
title: Läs mer om annan token och anspråkstyper som stöds av Azure AD | Microsoft Docs
description: En guide för att förstå och utvärdera anspråk i SAML 2.0- och JSON Web token (JWT)-token som utfärdas av Azure Active Directory (AAD)
documentationcenter: na
author: CelesteDG
services: active-directory
manager: mtillman
editor: ''
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/22/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a8d4f2894a188ce83939180def65c5f5b058d215
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902220"
---
# <a name="azure-ad-token-reference"></a>Tokenreferens för Azure AD
Azure Active Directory (Azure AD) genererar flera typer av säkerhetstoken under bearbetningen av varje autentiseringsflödet. Det här dokumentet beskriver format, security egenskaperna och innehållet i varje typ av token. 

## <a name="types-of-tokens"></a>Typer av token
Azure AD stöder den [OAuth 2.0-protokollet för auktorisering](v1-protocols-oauth-code.md), som utnyttjar både access_tokens och refresh_tokens. Det stöder också autentisering och inloggning via [OpenID Connect](v1-protocols-openid-connect-code.md), som introducerar en tredje typ av token, id_token. Var och en av dessa token representeras som en ”ägartoken”.

En ägartoken är en förenklad säkerhetstoken som ger ”ägar” åtkomst till en skyddad resurs. I detta avseende är ”ägar” en part som kan utgöra en token. Även om autentisering med Azure AD krävs för att få en ägartoken, vidtas åtgärder för att skydda token för att förhindra avlyssning av en oavsiktlig part. Eftersom ägar-token inte har en inbyggd mekanism för att förhindra att obehöriga personer från att använda dem, måste de transporteras i en säker kanal, till exempel transport layer security (HTTPS). Om en ägartoken skickas i klartext, kan en man-modul i mitten-attack användas för att hämta token och få obehörig åtkomst till en skyddad resurs. Samma säkerhetsprinciper gäller när du lagrar eller cachelagring ägar-token för senare användning. Se alltid till att appen överför och lagrar ägar-token på ett säkert sätt. Mer säkerheten på ägartoken, finns [RFC 6750 avsnitt 5](http://tools.ietf.org/html/rfc6750).

Många av de token som utfärdas av Azure AD implementeras som JSON Web token eller JWTs. En JWT är ett kompakt, URL-säkert sätt att överföra information mellan två parter. Informationen i JWTs kallas ”anspråk” eller intyg för information om ägar- och ämnet för token. Anspråk i JWTs är JSON-objekt kodade och serialiseras för överföring. Eftersom JWTs som utfärdats av Azure AD är signerad, men inte är krypterade, kan du enkelt granska innehållet i en JWT för felsökning. Det finns flera verktyg för att göra det, till exempel [jwt.ms](https://jwt.ms/). Mer information om JWTs kan du referera till den [JWT-specifikationen](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html).

## <a name="idtokens"></a>Id_tokens
Id_tokens är en typ av inloggning säkerhetstoken som din app tar emot när du utför autentisering med [OpenID Connect](v1-protocols-openid-connect-code.md). De representeras som [JWTs](#types-of-tokens), och innehåller anspråk som du kan använda för att signera användaren i din app. Du kan använda anspråken i en id_token som du vill – vanligtvis de används för att visa kontoinformation eller gör besluten om åtkomstkontroll i en app.

Id_tokens är signerad, men inte krypterat just nu. När din app tar emot en id_token, måste den [källpaketets signatur](#validating-tokens) token för autentisering och validera några anspråken i token för att bevisa dess giltighet. De anspråk som verifierats av en app varierar beroende på krav, men det finns några [vanliga anspråk verifieringar](#validating-tokens) som din app måste utföra i alla scenarier.

Se följande avsnitt innehåller information på id_tokens anspråk, samt en exempel-id_token. Observera att anspråk i id_tokens inte returneras i någon särskild ordning. Dessutom kan nya anspråk kan införas i id_tokens när som helst i tid – din app ska inte skadar när nya anspråk introduceras. Följande lista innehåller de anspråk som din app på ett tillförlitligt sätt kan tolka när detta skrivs. Om behövs ännu fler detaljer finns i den [OpenID Connect-specifikationen](http://openid.net/specs/openid-connect-core-1_0.html).

#### <a name="sample-idtoken"></a>Exemplet id_token
```
eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctODkwYS0yNzRhNzJhNzMwOWUiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83ZmU4MTQ0Ny1kYTU3LTQzODUtYmVjYi02ZGU1N2YyMTQ3N2UvIiwiaWF0IjoxMzg4NDQwODYzLCJuYmYiOjEzODg0NDA4NjMsImV4cCI6MTM4ODQ0NDc2MywidmVyIjoiMS4wIiwidGlkIjoiN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlIiwib2lkIjoiNjgzODlhZTItNjJmYS00YjE4LTkxZmUtNTNkZDEwOWQ3NGY1IiwidXBuIjoiZnJhbmttQGNvbnRvc28uY29tIiwidW5pcXVlX25hbWUiOiJmcmFua21AY29udG9zby5jb20iLCJzdWIiOiJKV3ZZZENXUGhobHBTMVpzZjd5WVV4U2hVd3RVbTV5elBtd18talgzZkhZIiwiZmFtaWx5X25hbWUiOiJNaWxsZXIiLCJnaXZlbl9uYW1lIjoiRnJhbmsifQ.
```

> [!TIP]
> Du bör testa i Granska anspråk i exemplet id_token genom att klistra in den i [jwt.ms](https://jwt.ms/).
> 
> 

#### <a name="claims-in-idtokens"></a>Anspråk i id_tokens
> [!div class="mx-codeBreakAll"]
| JWT-anspråk | Namn | Beskrivning |
| --- | --- | --- |
| `aud` |Målgrupp |Den avsedda mottagaren av token. Det program som tar emot en token måste kontrollera att värdet för målgrupp är korrekt och avvisa de token som är avsedd för en annan publik. <br><br> **SAML exempelvärde**: <br> `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>` <br><br> **Exempelvärde JWT**: <br> `"aud":"https://contoso.com"` |
| `appidacr` |Application Authentication Context Class Reference |Anger hur klienten autentiserades. Värdet är 0 för en offentlig klient. Om klient-ID och klienthemlighet används är värdet 1. Om ett klientcertifikat har använts för autentisering, är värdet 2. <br><br> **Exempelvärde JWT**: <br> `"appidacr": "0"` |
| `acr` |Authentication Context Class Reference |Anger hur ämnet autentiserades, till skillnad från klienten i anspråket Application Authentication Context Class Reference. Värdet ”0” anger slutanvändarens autentisering inte uppfyllde kraven för ISO/IEC 29115. <br><br> **Exempelvärde JWT**: <br> `"acr": "0"` |
| Autentiseringstillfälle |Innehåller information om datum och tid när autentiseringen gjordes. <br><br> **SAML exempelvärde**: <br> `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | |
| `amr` |Autentiseringsmetod |Identifierar hur ämnet för token autentiserades. <br><br> **SAML exempelvärde**: <br> `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` <br><br> **Exempelvärde JWT**: `“amr”: ["pwd"]` |
| `given_name` |Förnamn |Innehåller först eller ”förnamn” för användaren som Azure AD user-objektet. <br><br> **SAML exempelvärde**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”>`<br>`<AttributeValue>Frank<AttributeValue>` <br><br> **Exempelvärde JWT**: <br> `"given_name": "Frank"` |
| `groups` |Grupper |Innehåller objekt-ID som representerar användarens gruppmedlemskap. Dessa värden är unikt (se objekt-ID) och kan användas på ett säkert sätt för att hantera åtkomst till exempel framtvinga behörighet att komma åt en resurs. De grupper som ingår i grupper-anspråket är konfigurerade på basis av per program, via egenskapen ”groupMembershipClaims” i programmanifestet. En null-värde ska undanta alla grupper, värdet ”Tilldelningsmodulen” innehåller endast Active Directory-säkerhetsgrupp-medlemskap och ett värde av ”alla” kommer inkludera säkerhetsgrupper och distributionslistor för Office 365. <br><br> **Anteckningar**: <br> Se den `hasgroups` anspråk nedan för information om hur du använder den `groups` anspråk med implicit beviljande. <br> För andra flöden, om antalet grupper som användaren är i går via en gräns (150 för SAML, 200 för JWT) och sedan överförbrukning anspråk läggs de anspråk källor som pekar på Graph-slutpunkt som innehåller listan över grupper för användaren. (i. <br><br> **SAML exempelvärde**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` <br><br> **Exempelvärde JWT**: <br> `“groups”: ["0e129f5b-6b0a-4944-982d-f776045632af", … ]` |
|`hasgroups` | JWT Implicit flöde grupper överförbrukning indikator| Om det finns alltid `true`, anger användaren finns i minst en grupp. Används i stället för den `groups` anspråk för JWTs i implicit beviljande av flöden om fullständig grupper anspråk kan utökas URI-fragment överskrider gränserna för URL-längd (för närvarande 6 eller flera grupper). Anger att klienten ska använda i diagrammet för att fastställa användarens grupper (`https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects`). |
| `groups:src1` <br> `http://schemas.microsoft.com/claims/groups.link` | Överförbrukning indikator för grupper | För tokenbegäranden som inte är begränsad längd (se `hasgroups` ovan) men fortfarande är för stor för token en länk till fullständiga listan över för användaren ska inkluderas. För JWTs som en distribuerad anspråk för SAML som ett nytt anspråk i stället för den `groups` anspråk. <br><br> **SAML exempelvärde**: <br> `<Attribute Name=” http://schemas.microsoft.com/claims/groups.link”>`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` <br><br> **Exempelvärde JWT**: <br> `"groups":"src1` <br> `_claim_sources`: `"src1" : { "endpoint" : "https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects" }`|
| `idp` |Identitetsprovider |Registrerar den identitetsprovider som har autentiserat subjektet för token. Det här värdet är identiskt med värdet för utfärdaren anspråk, såvida inte användarkontot finns i en annan klient än utfärdaren. <br><br> **SAML exempelvärde**: <br> `<Attribute Name=” http://schemas.microsoft.com/identity/claims/identityprovider”>`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` <br><br> **Exempelvärde JWT**: <br> `"idp":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `iat` |IssuedAt |Lagrar den tid då token utfärdats. Det används ofta för att mäta token färskhet. <br><br> **SAML exempelvärde**: <br> `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` <br><br> **Exempelvärde JWT**: <br> `"iat": 1390234181` |
| `iss` |Utfärdare |Identifierar den säkerhetstokentjänst (STS) som skapar och returnerar token. I de token som Azure AD returnerar är utfärdaren sts.windows.net. GUID i anspråksvärdet utfärdare är klient-ID för Azure AD-katalog. Klient-ID är en identifierare som kan ändras och tillförlitlig för katalogen. <br><br> **SAML exempelvärde**: <br> `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` <br><br> **Exempelvärde JWT**: <br>  `"iss":”https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/”` |
| `family_name` |Efternamn |Innehåller senaste namn, efternamn eller namn för användaren som definieras i Azure AD-användarobjektet. <br><br> **SAML exempelvärde**: <br> `<Attribute Name=” http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”>`<br>`<AttributeValue>Miller<AttributeValue>` <br><br> **Exempelvärde JWT**: <br> `"family_name": "Miller"` |
| `unique_name` |Namn |Innehåller ett läsbart värde som identifierar subjektet för token. Det här värdet är inte säkert att vara unika inom en klient och är avsedd att användas endast för visning. <br><br> **SAML exempelvärde**: <br> `<Attribute Name=”http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name”>`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>` <br><br> **Exempelvärde JWT**: <br> `"unique_name": "frankm@contoso.com"` |
| `oid` |Objekt-ID |Innehåller en unik identifierare för ett objekt i Azure AD. Det här värdet kan inte ändras och det går inte att tilldela om eller återanvänds. Använda objekt-ID för att identifiera ett objekt i frågor till Azure AD. <br><br> **SAML exempelvärde**: <br> `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` <br><br> **Exempelvärde JWT**: <br> `"oid":"528b2ac2-aa9c-45e1-88d4-959b53bc7dd0"` |
| `roles` |Roller |Representerar alla programroller som ämnet har beviljats direkt eller indirekt via gruppmedlemskap och kan användas för att tillämpa rollbaserad åtkomstkontroll. Programroller definieras på basis av per program, via den `appRoles` egenskapen för programmanifestet. Den `value` egenskapen för varje programroll är det värde som visas i rollanspråk. <br><br> **SAML exempelvärde**: <br> `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`<br>`<AttributeValue>Admin</AttributeValue>` <br><br> **Exempelvärde JWT**: <br> `“roles”: ["Admin", … ]` |
| `scp` |Omfång |Anger personifiering behörigheterna till klientprogrammet. Standardbehörigheten är `user_impersonation`. Ägaren till den skyddade resursen kan registrera ytterligare värden i Azure AD. <br><br> **Exempelvärde JWT**: <br> `"scp": "user_impersonation"` |
| `sub` |Subjekt |Identifierar huvudnamn som token kontrollerar information, t.ex användare av ett program. Det här värdet kan inte ändras och kan inte tilldelas eller återanvänds, så den kan användas för att utföra auktoriseringskontroller på ett säkert sätt. Eftersom ämnet alltid finns i token som Azure AD-problem rekommenderar vi med det här värdet i ett system för auktorisering av generell användning. <br> `SubjectConfirmation` är inte ett anspråk. Beskriver hur ämnet för token har verifierats. `Bearer` Anger att ämnet har bekräftats av tillgång till dem i token. <br><br> **SAML exempelvärde**: <br> `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>` <br><br> **Exempelvärde JWT**: <br> `"sub":"92d0312b-26b9-4887-a338-7b00fb3c5eab"` |
| `tid` |Klient-ID:t |En oföränderlig, icke-återanvändningsbara ID som identifierar den directory-klient som utfärdade token. Du kan använda det här värdet till klientspecifik directory resurser i ett program med flera innehavare. Du kan till exempel använda det här värdet för att identifiera klienten i ett anrop till Graph API. <br><br> **SAML exempelvärde**: <br> `<Attribute Name=”http://schemas.microsoft.com/identity/claims/tenantid”>`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>` <br><br> **Exempelvärde JWT**: <br> `"tid":"cbb1a5ac-f33b-45fa-9bf5-f37db0fed422"` |
| `nbf`, `exp` |Tokenlivstid |Definierar tidsintervallet då token är giltig. Den tjänst som verifierar token bör kontrollera att det aktuella datumet ligger inom livslängd för token, annan det avvisar token. Tjänsten kan göra att överskrida livslängd för uppdateringstoken upp till fem minuter att kompensera för eventuella skillnader i clock-tid (”tid skeva”) mellan Azure AD och tjänsten. <br><br> **SAML exempelvärde**: <br> `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br><br> **Exempelvärde JWT**: <br> `"nbf":1363289634, "exp":1363293234` |
| `upn` |User Principal Name |Lagrar användarnamnet för användaren.<br><br> **Exempelvärde JWT**: <br> `"upn": frankm@contoso.com` |
| `ver` |Version |Lagrar det lägre versionsnumret för token. <br><br> **Exempelvärde JWT**: <br> `"ver": "1.0"` |

## <a name="access-tokens"></a>Åtkomsttokens
Efter lyckad autentisering returnerar en åtkomsttoken som kan användas för att komma åt skyddade resurser i Azure AD. Åtkomsttoken är en base 64-kodad JSON Web Token (JWT) och dess innehåll kan granskas genom att köra det via en avkodare.

Om din app bara *använder* åtkomsttoken för att få åtkomst till API: er, du kan (och bör) du behandla åtkomsttoken för som helt täckande – de är bara strängar som din app kan skicka till resurser i HTTP-begäranden.

När du begär en åtkomsttoken returnerar Azure AD även vissa metadata om åtkomsttoken för användning av din app. Informationen omfattar förfallotiden för åtkomst-token och de omfattningar som den är giltig. På så sätt kan din app att utföra intelligent cachelagring av åtkomsttoken utan att behöva parsa öppna den åtkomst-token.

Om din app är en API som skyddas med Azure AD som förväntar sig åtkomsttoken i HTTP-förfrågningar, bör sedan du utföra verifiering och granskning av de token som du får. Din app ska utföra verifiering av åtkomst-token innan du använder det för att få åtkomst till resurser. Mer information om verifiering finns i [verifierar token](#validating-tokens). Mer information om hur du gör detta med .NET finns [skydda ett webb-API med ägar-token från Azure AD](quickstart-v1-dotnet-webapi.md).

## <a name="refresh-tokens"></a>Uppdatera token

Uppdatera token är säkerhetstoken som din app kan använda för att hämta nya åtkomsttoken i ett OAuth 2.0-flöde. Det gör din app för att uppnå långsiktig åtkomst till resurser för en användares räkning utan interaktion med användaren.

Uppdateringstoken är flera resurs. Det vill säga lösas att en uppdateringstoken som togs emot under en Tokenbegäran för en resurs kan in för åtkomst-token till en helt annan resurs. Om du vill göra detta måste du ange den `resource` parameter i förfrågan om att resurs.

Uppdateringstoken är fullständigt täckande för din app. De är långlivade, men appen inte att skriva förväntar sig att varar en uppdateringstoken för varje tidsperiod. Uppdateringstoken kan vara inaktuella när som helst för en mängd orsaker – Se [återkallningen av Token](#token-revocation) därmed. Det enda sättet för din app att veta om en uppdateringstoken är giltig är att försöka lösa in den genom att göra en begäran om token till Azure AD-token-slutpunkten.

När du lösa in en uppdateringstoken för en ny åtkomsttoken visas en ny uppdateringstoken i token-svaret. Du bör spara nyligen utfärdade uppdateringstoken ersätter den du använde i begäran. Detta garanterar att dina uppdateringstoken förblir giltigt så länge som möjligt.

## <a name="validating-tokens"></a>Verifiera token

För att kunna verifiera en id_token eller en access_token, bör din app verifiera både signaturen för den token och anspråk. För att kunna verifiera åtkomsttoken, bör din app också verifiera utfärdaren, målgruppen och signering token. Dessa måste verifieras mot värdena i OpenID discovery-dokumentet. Till exempel den oberoende klient-versionen av dokumentet finns på [ https://login.microsoftonline.com/common/.well-known/openid-configuration ](https://login.microsoftonline.com/common/.well-known/openid-configuration). Azure AD-mellanprogram har inbyggda funktioner för att verifiera åtkomsttoken och du kan bläddra igenom vår [exempel](https://docs.microsoft.com/azure/active-directory/active-directory-code-samples) i valfritt språk. Mer information om hur du uttryckligen validerar en JWT-token finns i den [manuell JWT-Verifieringsexempel](https://github.com/Azure-Samples/active-directory-dotnet-webapi-manual-jwt-validation). 

Vi tillhandahåller bibliotek och kodexempel som visar hur du enkelt hantera tokenvalidering - den informationen nedan tillhandahålls bara för de som vill förstå underliggande processen. Det finns också flera bibliotek med öppen källkod från tredje part för JWT-verifiering – det finns minst ett alternativ för nästan alla plattformar och språk där. Mer information om Azure AD-autentiseringsbibliotek och exempel finns i [Azure AD-autentiseringsbibliotek](active-directory-authentication-libraries.md).

#### <a name="validating-the-signature"></a>Verifiera signaturen

En JWT innehåller tre segment som avgränsas med den `.` tecken. Det första segmentet kallas den **rubrik**, andra som den **brödtext**, och tredje som den **signatur**. Signatur-segment kan användas för att verifiera äkthet token så att det kan vara betrott av din app.

Token som utfärdas av Azure AD har signerats med hjälp av bransch standard asymmetriska krypteringsalgoritmer, till exempel RSA-256. Rubriken för JWT innehåller information om metoden nyckel och kryptering används för att signera token:

```
{
  "typ": "JWT",
  "alg": "RS256",
  "x5t": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
  "kid": "iBjL1Rcqzhiy4fpxIxdZqohM2Yk"
}
```

Den `alg` anspråk anger algoritmen som används för att signera token, medan den `x5t` anspråk anger den specifika offentliga nyckel som användes för att signera token.

Vid en given tidpunkt i tid, kan Azure AD logga en id_token som använder någon av en viss uppsättning offentligt / privat nyckelpar. Azure AD roterar möjliga uppsättning nycklar regelbundet, så att din app ska skrivas till hanterar dessa viktiga ändringar automatiskt. En rimlig frekvens för att söka efter uppdateringar till de offentliga nycklar som används av Azure AD är 24 timmar.

Du kan hämta signering viktiga data som behövs för att verifiera signaturen med hjälp av webbplatsen för OpenID Connect metadata som finns på:

```
https://login.microsoftonline.com/common/.well-known/openid-configuration
```

> [!TIP]
> Prova den här URL: en i en webbläsare!

Det här Metadatadokumentet är ett JSON-objekt som innehåller flera olika användbara typer av information, t.ex platsen för de olika slutpunkter som krävs för att utföra autentisering med OpenID Connect. 

Den innehåller också en `jwks_uri`, vilket ger platsen för en uppsättning offentliga nycklar som används för att signera token. JSON-dokumentet finns på den `jwks_uri` innehåller alla informationen om offentliga nyckeln används av den viss tidpunkten. Din app kan använda den `kid` anspråk i JWT-huvudet för att välja vilka offentliga nyckeln i det här dokumentet har använts för att logga en viss token. Den kan sedan utföra signaturverifiering med rätt offentlig nyckel och den angivna algoritmen.

> [!NOTE]
> V1.0 slutpunkten returnerar både den `x5t` och `kid` anspråk. Den `x5t` -anspråk saknas från v2.0-token. V2.0-slutpunkten svarar med den `kid` anspråk. Framöver kommer vi rekommenderar att du använder den `kid` anspråk som ska verifiera din token.

Utför signaturverifiering ligger utanför omfånget för det här dokumentet – det finns många bibliotek med öppen källkod för att hjälpa dig göra det om det behövs.

#### <a name="validating-the-claims"></a>Verifiera anspråk

När din app tar emot en token (antingen en id_token vid användarinloggning eller en åtkomsttoken som en ägartoken i HTTP-begäran) bör det också utföra några kontroller mot anspråken i token. Dessa inkludera, men är inte begränsade till:

* Den **målgrupp** anspråk – Kontrollera att token ska tilldelas till din app.
* Den **inte före** och **förfallotid** anspråk – Kontrollera att token inte har gått ut.
* Den **utfärdare** anspråk – Kontrollera att token verkligen har utfärdats till din app av Azure AD.
* Den **Nonce** – om du vill minimera en token repetitionsattacker-attack.
* med mera...

En fullständig lista över anspråk verifieringar din app ska utföra för ID-token finns i den [OpenID Connect-specifikationen](http://openid.net/specs/openid-connect-core-1_0.html#IDTokenValidation). Information om de förväntade värdena för dessa anspråk som ingår i det föregående [id_token](#id-tokens) avsnittet.

## <a name="token-revocation"></a>Återkallningen av token

Uppdatera token kan vara ogiltig eller återkallas när som helst av olika skäl. Dessa är indelade i två huvudsakliga kategorier: tidsgränser och återkallelse. 

**Token tidsgränser**

* MaxInactiveTime: Om uppdateringstoken inte har använts inom den tiden enligt MaxInactiveTime kan uppdatera Token inte längre giltig. 
* MaxSessionAge: Om du har angett MaxAgeSessionMultiFactor eller MaxAgeSessionSingleFactor till något annat än standard (tills-återkallas), sedan återautentiseringen måste utföras efter den i MaxAgeSession * tiden. 
* Exempel:
  * Klienten har en MaxInactiveTime på fem dagar och användaren gick på semester för en vecka och så AAD har inte sett en ny token begäran från användaren i 7 dagar. Nästa gång användaren begär en ny token hittar de sina uppdatera Token har återkallats och de måste ange sina autentiseringsuppgifter igen. 
  * Ett känsliga program har en MaxAgeSessionSingleFactor på 1 dag. Om en användare loggar in på måndag och tisdagen (efter 25 timmar har gått), kommer de att behöva autentisera igen. 

**Återkallade certifikat**

|   | Lösenordsbaserad cookie | Lösenordsbaserad token | Icke-password baserat cookie | Icke-password baserat token | Konfidentiell klient-token| 
|---|-----------------------|----------------------|---------------------------|--------------------------|--------------------------|
|Lösenordet upphör att gälla| Förblir aktiv|Förblir aktiv|Förblir aktiv|Förblir aktiv|Förblir aktiv|
|Lösenordet har ändrats av användaren| Återkallad | Återkallad | Förblir aktiv|Förblir aktiv|Förblir aktiv|
|Användare utför SSPR|Återkallad | Återkallad | Förblir aktiv|Förblir aktiv|Förblir aktiv|
|Återställer lösenordet för serveradministratören|Återkallad | Återkallad | Förblir aktiv|Förblir aktiv|Förblir aktiv|
|Användaren återkallar sina uppdateringstoken [via PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Återkallad | Återkallad |Återkallad | Återkallad |Återkallad | Återkallad |
|Administratör återkallar alla uppdateringstoken för klienten [via PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Återkallad | Återkallad |Återkallad | Återkallad |Återkallad | Återkallad |
|[Enkel inloggning ut](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code#single-sign-out) på webben | Återkallad | Förblir aktiv |Återkallad | Förblir aktiv |Förblir aktiv |Förblir aktiv |

> [!NOTE]
> En inloggning som ”icke-password baserad” är en där användaren inte ange ett lösenord för att hämta den.  Med exempelvis ansiktet med Windows Hello, en FIDO-nyckel eller en PIN-kod. 
>
> Det finns ett känt problem med den Windows primära uppdatera Token.  Om PRT hämtas via ett lösenord, och sedan en användare loggar in via Hello, ursprunget av PRT ändras inte och kommer att återkallas om användaren ändrar sitt lösenord. 

## <a name="sample-tokens"></a>Exempel-token

Det här avsnittet visar exempel på SAML- och JWT-token som returnerar Azure AD. Dessa exempel kan du se anspråk i kontexten.

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

### <a name="jwt-token---user-impersonation"></a>JWT-Token - personifiering av användare
Det här är ett exempel på ett typiskt JSON-webbtoken (JWT) som används i en grant-auktoriseringskodflöde.
Förutom anspråk, token innehåller ett versionsnummer i **ver** och **appidacr**, autentisering kontext klass referens, som anger hur klienten autentiserades. Värdet är 0 för en offentlig klient. Om en klient-ID eller klienthemlighet har använts, är värdet 1.

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
* Finns i Azure AD Graph [principåtgärder](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) och [principentiteten](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity)du vill veta mer om hur du hanterar principer för livslängd för token via Azure AD Graph API.
* Mer information och exempel om hur du hanterar principer via PowerShell-cmdletar, inklusive exempel, finns i [konfigurerbara tokenlivslängder i Azure AD](active-directory-configurable-token-lifetimes.md). 
* Lägg till [anpassade och valfria anspråk](active-directory-optional-claims.md) till token för ditt program. 
