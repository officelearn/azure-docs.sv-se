---
title: Översikt över token - Azure Active Directory B2C | Microsoft Docs
description: Läs mer om de token som används i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 11361bc6ab75e873e1b4081dcfc6492abc093b54
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680272"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Översikt över token i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD) B2C skickar flera typer av säkerhetstoken när den går igenom varje [autentiseringsflödet](active-directory-b2c-apps.md). Det här dokumentet beskriver format, security egenskaperna och innehållet i varje typ av token.

## <a name="token-types"></a>Typer av token

Azure AD-B2C stöder den [OAuth 2.0 och OpenID Connect-protokoll](active-directory-b2c-reference-protocols.md), som utnyttjar token för autentisering och säker åtkomst till resurser. Alla token som används i Azure AD B2C är [JSON web token (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) som innehåller intyg om innehavaren och ämnet för token.

Följande variabler används vid kommunikation med Azure AD B2C:

- *ID-token* -A-JWT som innehåller anspråk som du kan använda för att identifiera användare i ditt program. Denna token skickas på ett säkert sätt i HTTP-begäranden för kommunikation mellan två komponenter i samma programmet eller tjänsten. Du kan använda anspråken i en ID-token som du vill. De används ofta att visa kontoinformation eller gör besluten om åtkomstkontroll i ett program. ID-token är signerade, men de inte är krypterade. När ditt program eller API får du en ID-token, måste det verifiera signaturen för att bevisa att token är autentiska. Programmet eller API: et måste du också bekräfta några anspråken i token för att bevisa att den är giltig. Anspråk av ett program kan variera beroende på scenariokrav, men programmet måste utföra några vanliga anspråk verifieringar i alla scenarier.
- *Åtkomsttoken* -A-JWT som innehåller anspråk som du kan använda för att identifiera de beviljade behörigheterna till dina API: er. Åtkomsttoken är inloggad, men de är inte krypterat. Åtkomsttoken används för att ge åtkomst till API: er och resurs-servrar.  När ditt API får en åtkomst-token, måste det verifiera signaturen för att bevisa att token är autentiska. Ditt API måste också bekräfta några anspråken i token för att bevisa att den är giltig. Anspråk av ett program kan variera beroende på scenariokrav, men programmet måste utföra några vanliga anspråk verifieringar i alla scenarier.
- *Uppdatera token* -uppdateringstoken som används för att hämta nya ID-token och åtkomsttoken i ett OAuth 2.0-flöde. De ger ditt program med långsiktig åtkomst till resurser för användare utan interaktion med användare. Uppdateringstoken är täckande för ditt program. De kan har utfärdats av Azure AD B2C och granskas och tolkas bara av Azure AD B2C. De är långlivade, men ditt program bör inte skrivas med förväntan om att en uppdateringstoken varar en viss tidsperiod. Uppdateringstoken kan vara inaktuella när som helst av olika skäl. Det enda sättet för att appen ska veta om en uppdateringstoken är giltig är att försöka lösa in den genom att göra en begäran om token till Azure AD B2C. När du har löst in en uppdateringstoken för en ny token, får du en ny uppdateringstoken i token-svaret. Spara den nya uppdateringstoken. Den ersätter den uppdateringstoken som du tidigare använt i begäran. Den här åtgärden hjälper till att garantera att dina uppdateringstoken förblir giltigt så länge som möjligt. 

## <a name="endpoints"></a>Slutpunkter

En [registrerade program](tutorial-register-applications.md) tar emot token och kommunicerar med Azure AD B2C genom att skicka förfrågningar till dessa slutpunkter:

- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize`
- `https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token`

Säkerhetstoken som programmet tar emot från Azure AD B2C kan komma från den `/authorize` eller `/token` slutpunkter. När ID-token är upptagna från den `/authorize` slutpunkt kan de görs det med hjälp av den [implicit flöde](active-directory-b2c-reference-spa.md), vilket ofta används för användare som loggar in till javascript-baserade webbprogram. När ID-token är upptagna från den `/token` slutpunkt kan de görs det med hjälp av den [kod flow](active-directory-b2c-reference-oidc.md), som ser till att den token som är dolda från webbläsaren.

## <a name="claims"></a>Anspråk

När du använder Azure AD B2C har detaljerad kontroll över innehållet i dina token. Du kan konfigurera [användarflöden](active-directory-b2c-reference-policies.md) och [anpassade principer](active-directory-b2c-overview-custom.md) att skicka vissa uppsättningar av användardata i anspråk som krävs för ditt program. Dessa anspråk kan innehålla standardegenskaper som **displayName** och **e-postadress**. Dina program kan använda de här anspråken att på ett säkert sätt autentisera användare och förfrågningar. 

Anspråk i ID-token returneras inte i någon särskild ordning. Nya anspråk kan införas i ID-token när som helst. Ditt program bör inte bryter när nya anspråk introduceras. Du kan även inkludera [anpassade användarattribut](active-directory-b2c-reference-custom-attr.md) i din anspråk.

I följande tabell visas de anspråk som du förväntar dig av ID-token och komma åt token som utfärdas av Azure AD B2C.

| Namn | Begäran | Exempelvärde | Beskrivning |
| ---- | ----- | ------------- | ----------- |
| Målgrupp | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifierar den avsedda mottagaren av token. För Azure AD B2C är målgruppen program-ID. Ditt program bör verifiera det här värdet och avvisa token om det inte matchar. Målgruppen är synonyma med resursen. |
| Utfärdare | `iss` |`https://{tenant}.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifierar den säkerhetstokentjänst (STS) som skapar och returnerar token. Den identifierar även den katalog där användaren autentiserades. Ditt program bör verifiera utfärdare-anspråk för att se till att token kom från lämplig slutpunkt. |
| Utfärdat på | `iat` | `1438535543` | Den tid då token utfärdats representeras i epoktid. |
| Förfallotid | `exp` | `1438539443` | Den tid då token blir ogiltig, representeras i epoktid. Ditt program bör använda detta anspråk för att kontrollera giltigheten för livslängd för token. |
| Inte före | `nbf` | `1438535543` | Den tid då token som börjar gälla, representeras i epoktid. Nu är vanligtvis samma som den tid som token utfärdats. Ditt program bör använda detta anspråk för att kontrollera giltigheten för livslängd för token. |
| Version | `ver` | `1.0` | Versionen av ID-token, som definieras av Azure AD B2C. |
| Kod hash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | En kod-hash som ingår i en ID-token endast när token utfärdas tillsammans med en OAuth 2.0-auktoriseringskod. En kod hash kan användas för att bekräfta en auktoriseringskod är äkta. Läs mer om hur du utför den här verifieringen, den [OpenID Connect-specifikationen](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Åtkomst-token-hash | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | En åtkomst-token-hash ingår i en ID-token endast när token utfärdas tillsammans med en OAuth 2.0-åtkomsttoken. En åtkomst-token-hash kan användas för att bekräfta en åtkomst-token är äkta. Läs mer om hur du utför den här verifieringen, den [OpenID Connect-specifikation](https://openid.net/specs/openid-connect-core-1_0.html)  |
| nonce | `nonce` | `12345` | En nonce är en strategi för att åtgärda token repetitionsattacker. Programmet kan ange en nonce i en begäran om godkännande med hjälp av den `nonce` frågeparameter. Värdet du anger i begäran har genererats ska ändras i den `nonce` anspråk för en endast ID-token. Det här anspråket tillåter programmet att kontrollera värden mot det värde som angetts i förfrågan. Ditt program bör utföra den här verifieringen under verifieringsprocessen för ID-token. |
| Ämne | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Huvudkontot som token kontrollerar information, t.ex användare av ett program. Det här värdet kan inte ändras och det går inte att tilldela om eller återanvänds. Det kan användas för att utföra auktoriseringskontroller på ett säkert sätt, till exempel när token används för att komma åt en resurs. Som standard fylls anspråk för ämne med objekt-ID för användaren i katalogen. |
| Referens för autentisering kontext-klass | `acr` | Inte tillämpligt | Används endast med äldre principer. |
| Lita på framework-princip | `tfp` | `b2c_1_signupsignin1` | Namnet på den princip som användes för att hämta ID-token. |
| Autentisering | `auth_time` | `1438535543` | Den tid som en användare senast angivna autentiseringsuppgifter, representeras i epoktid. |
| Scope | `scp` | `Read`| Behörigheterna för resurs för en åtkomsttoken. Flera beviljade behörigheter avgränsas med ett blanksteg. |
| Behörig part | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | Det **program-ID** hos klientprogrammet som initierade begäran. |

## <a name="configuration"></a>Konfiguration

Följande egenskaper är vana vid [hantera livslängd av säkerhetstoken](configure-tokens.md) orsakats av Azure AD B2C:

- **Åtkomst och ID-token livstid (minuter)** – livslängden för OAuth 2.0-ägartoken som används för att få åtkomst till en skyddad resurs. Standardvärdet är 60 minuter. Minsta (inklusivt) är 5 minuter. Högsta (inklusivt) är 1 440 minuter.

- **Livslängd för uppdateringstoken (dagar)** – den maximala tidsperiod innan vilken en uppdateringstoken kan användas för att hämta en ny åtkomst- eller ID-token. Hur lång tid tar även upp en ny uppdateringstoken hämtades om programmet har beviljats den `offline_access` omfång. Standardvärdet är 14 dagar. Minsta (inklusivt) är en dag. Max (inklusivt) är 90 dagar.

- **Token livslängd för skjutfönster (dagar)** – när den här perioden tid är slut om användaren tvingas att autentiseras på nytt, oavsett giltighetsperioden hos den senaste uppdateringstoken som införskaffats av programmet. Det kan endast anges om växeln har angetts till **begränsad**. Det måste vara större än eller lika med den **livslängd för uppdateringstoken (dagar)** värde. Om växeln har angetts till **Ramavgränsare**, du kan inte ange ett specifikt värde. Standardvärdet är 90 dagar. Minsta (inklusivt) är en dag. Det högsta antalet (inklusivt) är 365 dagar.

Följande användningsfall aktiveras med hjälp av dessa egenskaper:

- Tillåt en användare att förbli inloggad till en hanteringsprincip för mobilprogram på obestämd tid, så länge användaren är aktiv hela tiden på programmet. Du kan ange **glidande fönstret livslängd för uppdateringstoken (dagar)** till **Ramavgränsare** i ditt flöde på användarens.
- Uppfyll din bransch säkerhets- och efterlevnadskrav genom att ange lämplig åtkomst tokenlivslängder.

De här inställningarna är inte tillgängliga för att återställa lösenord användarflöden. 

## <a name="compatibility"></a>Kompatibilitet

Följande egenskaper är vana vid [hantera tokenkompatibilitet](configure-tokens.md):

- **Utfärdaren (iss) anspråk** -den här egensapen identifierar den Azure AD B2C-klient som utfärdade token. Standardvärdet är `https://<domain>/{B2C tenant GUID}/v2.0/`. Värdet för `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` innehåller ID: N för både Azure AD B2C-klient och det användarflöde som användes i token-begäran. Om ditt program eller bibliotek behöver Azure AD B2C ska vara kompatibel med den [OpenID Connect Discovery 1.0-specifikationen](https://openid.net/specs/openid-connect-discovery-1_0.html), Använd det här värdet.

- **Anspråk för ämne (sub)** – den här egenskapen identifierar den entitet som token kontrollerar information. Standardvärdet är **ObjectID**, som fylls i `sub` anspråk i token med objekt-ID för användaren. Värdet för **stöds inte** finns bara för bakåtkompatibilitet. Vi rekommenderar att du växlar till **ObjectID** så fort du.

- **Anspråk som representerar princip-ID** -den här egensapen identifierar Anspråkstypen som namnet på principen som används i token begäran har fyllts i. Standardvärdet är `tfp`. Värdet för `acr` finns bara för bakåtkompatibilitet.

## <a name="pass-through"></a>Direkt

När en användarresa startar får en åtkomst-token från en identitetsprovider i Azure AD B2C. Azure AD B2C använder den token för att hämta information om användaren. Du [aktivera ett anspråk i ditt användarflöde](idp-pass-through-user-flow.md) eller [definiera ett anspråk i en egen princip](idp-pass-through-custom.md) och skicka token via till de program som du registrerar i Azure AD B2C. Programmet måste använda en [v2 användarflödet](user-flow-versions.md) att dra nytta av skicka token som ett anspråk.

Azure AD-B2C stöder för närvarande bara skicka åtkomst-token för OAuth 2.0 identitetsprovidrar, till exempel Facebook och Google. För alla andra identitetsleverantörer returneras anspråket tomt. 

## <a name="validation"></a>Validering

För att verifiera en token kan bör ditt program Kontrollera både signatur och anspråk i token. Många bibliotek med öppen källkod är tillgängliga för att verifiera JWTs, beroende på det språk du föredrar. Vi rekommenderar att du utforska dessa alternativ i stället för att implementera dina egna validering av logik.

### <a name="validate-signature"></a>Verifiera signatur

En JWT innehåller tre segment en *rubrik*, ett *brödtext*, och en *signatur*. Signatur-segment kan användas för att verifiera äkthet token så att det kan vara betrott av ditt program. Azure AD B2C-token signeras med hjälp av branschstandard asymmetriska krypteringsalgoritmer, till exempel RSA-256. 

Rubriken för token innehåller information om metoden nyckel och kryptering används för att signera token:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Värdet för den **algoritm** anspråk är den algoritm som används för att signera token. Värdet för den **barn** anspråk är den offentliga nyckeln som används för att signera token. Azure AD B2C kan logga en token med hjälp av någon av en uppsättning offentligt / privat nyckelpar vid en given tidpunkt. Azure AD B2C roterar möjliga uppsättning nycklar regelbundet. Ditt program ska skrivas till hanterar dessa viktiga ändringar automatiskt. En rimlig frekvens för att söka efter uppdateringar till de offentliga nycklar som används av Azure AD B2C är 24 timmar.

Azure AD B2C har en slutpunkt för OpenID Connect metadata. Med den här slutpunkten kan kan program begära information om Azure AD B2C vid körning. Informationen omfattar slutpunkter, token innehåll och nycklar för tokensignering. Din Azure AD B2C-klient innehåller ett JSON-dokument för metadata för varje princip. Metadatadokumentet är ett JSON-objekt som innehåller flera användbara uppgifter för. Metadata innehåller **jwks_uri**, vilket ger platsen för en uppsättning offentliga nycklar som används för att signera token. Att platsen anges här, men det är bäst att hämta platsen dynamiskt med hjälp av webbplatsen för metadata och parsning **jwks_uri**:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/discovery/v2.0/keys?p=b2c_1_signupsignin1
```
JSON-dokument som finns på denna URL innehåller alla offentliga viktig information som används på en viss tidpunkt. Din app kan använda den `kid` anspråk i JWT-huvudet för att välja den offentliga nyckeln i JSON-dokument som används för att logga en viss token. Den kan sedan utföra signaturverifiering genom att använda rätt offentlig nyckel och den angivna algoritmen.

I Metadatadokumentet om i `B2C_1_signupsignin1` principen i den `contoso.onmicrosoft.com` -klient finns på:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=b2c_1_signupsignin1
```

Att avgöra vilken princip som användes för att registrera en token (och var du kan begära metadata), har du två alternativ. Först namnet på principen som ingår i den `acr` anspråk i token. Du kan parsa anspråk från innehållet i JWT av Base64-avkodning brödtexten och avserialisering av JSON-strängen som är ett resultat. Den `acr` anspråk är namnet på den princip som användes för att utfärda token. Ett annat alternativ är att koda principen i värdet för den `state` parameter när du skickar en begäran och avkoda det för att avgöra vilken princip som har använts. Någon av metoderna är giltig.

En beskrivning av hur du utför signaturverifiering ligger utanför omfånget för det här dokumentet. Många bibliotek med öppen källkod är tillgängliga för att hjälpa dig att validera en token.

### <a name="validate-claims"></a>Validera krav

När dina program eller API får du en ID-token, bör det också utföra flera kontroller mot anspråken i ID-token. Följande anspråk bör kontrolleras:

- **publik** – verifierar att ID-token ska tilldelas till ditt program.
- **inte före** och **förfallotid** – verifierar att ID-token inte har gått ut.
- **utfärdaren** – verifierar att token har utfärdats till programmet av Azure AD B2C.
- **nonce** -en strategi för token repetitionsattacker nätverksattacker.

En fullständig lista över verifieringar som ditt program ska utföra, finns det [OpenID Connect-specifikationen](https://openid.net).  

## <a name="next-steps"></a>Nästa steg

Mer information om hur du [använder åtkomsttoken](active-directory-b2c-access-tokens.md).

