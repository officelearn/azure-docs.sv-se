<properties
    pageTitle="Azure AD B2C Preview | Microsoft Azure"
    description="De typer av program som du kan skapa i Azure Active Directory B2C Preview."
    services="active-directory-b2c"
    documentationCenter=""
    authors="dstrockis"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="05/31/2016"
    ms.author="dastrock"/>

# Azure Active Directory B2C Preview: Typer av program

Azure AD (Active Directory Azure) B2C stöder autentisering för en rad olika moderna apparkitekturer. Alla baseras på standardprotokollen [OAuth 2.0](active-directory-b2c-reference-protocols.md) och [OpenID Connect](active-directory-b2c-reference-protocols.md). I det här dokumentet beskrivs kortfattat de olika typer av appar som du kan skapa, oberoende av plattform eller språk. Du får också förståelse för de övergripande scenarierna innan du [börjar utveckla program](active-directory-b2c-overview.md#getting-started).

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Grunderna
Alla appar som använder Azure AD B2C måste vara registrerade i din [B2C-katalog](active-directory-b2c-get-started.md) via [Azure Portal](https://portal.azure.com/). Registreringsprocessen samlar in och tilldelar några värden till din app:

- Ett **program-ID** som identifierar din app unikt.
- En **omdirigerings-URI** som kan användas för att dirigera svar tillbaka till din app.
- Eventuella andra scenariespecifika värden. Om du vill ha mer information lär du dig hur du [registrerar en app](active-directory-b2c-app-registration.md).

När appen har registrerats kommunicerar den med Azure AD genom att skicka förfrågningar till Azure AD v2.0-slutpunkten:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Varje begäran som skickas till Azure AD B2C anger en **princip**. En princip styr beteendet i Azure AD. Du kan också använda dessa slutpunkter för att skapa ytterst anpassningsbara användarupplevelser. Exempel på vanliga principer är registrerings-, inloggnings- och profilredigeringsprinciper. Om du inte är bekant med principer bör du lära dig om [det expanderbara principramverket](active-directory-b2c-reference-policies.md) för Azure AD B2C innan du fortsätter.

Interaktionen för alla appar med en v2.0-slutpunkt följer ett liknande övergripande mönster:

1. Appen dirigerar användaren till v2.0-slutpunkten för att köra en [princip](active-directory-b2c-reference-policies.md).
2. Användaren uppfyller principen enligt principdefinitionen.
4. Appen tar emot någon typ av säkerhetstoken från v2.0-slutpunkten.
5. Appen använder säkerhetstoken för att komma åt känslig information eller en skyddad resurs.
6. Resursservern verifierar säkerhetstoken för att kontrollera att åtkomst kan beviljas.
7. Appen uppdaterar säkerhetstoken med jämna mellanrum.

<!-- TODO: Need a page for libraries to link to -->
Anvisningarna kan skilja sig något beroende på vilken typ av app som du utvecklar. Du kan ta hjälp av bibliotek med öppen källkod.

## Webbappar
För webbappar (inklusive .NET, PHP, Java, Ruby, Python och Node.js) som finns på en server och öppnas via en webbläsare stöder Azure AD B2C [OpenID Connect](active-directory-b2c-reference-protocols.md) i alla användarmiljöer, till exempel inloggning, registrering och profilhantering. I Azure AD B2C-implementeringen av OpenID Connect initierar din webbapp dessa användarupplevelser genom att utfärda autentiseringsförfrågningar till Azure AD. Resultatet av begäran är en `id_token`. Den här säkerhetstoken representerar användarens identitet. Den tillhandahåller även information om användaren i form av anspråk:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Mer information om vilka typer av token och anspråk som är tillgängliga för en app finns i [referens för B2C-token](active-directory-b2c-reference-tokens.md).

I en webbapp utförs följande övergripande steg för varje körning av en [princip](active-directory-b2c-reference-policies.md):

![Bild i spaltformat som illustrerar ett webbappsflöde](./media/active-directory-b2c-apps/webapp.png)

Valideringen av `id_token` med hjälp av en offentlig signeringsnyckel som fås från Azure AD är tillräckligt för att verifiera användarens identitet. Åtgärden konfigurerar även en sessions-cookie som kan användas för att identifiera användaren vid efterföljande sidförfrågningar.

Om du vill se hur det här scenariot fungerar i praktiken provar du något av kodexemplen för inloggning med webbappar i [Komma igång-avsnittet](active-directory-b2c-overview.md#getting-started).

Förutom att underlätta enkel inloggning kan en webbserverapp också behöva åtkomst till en backend-webbtjänst. I detta fall kan webbappen köra ett något annorlunda [OpenID Connect-flöde](active-directory-b2c-reference-oidc.md) och hämta token genom att använda auktoriseringskoder och uppdateringstoken. Det här scenariot illustreras i följande avsnitt om [webb-API:er](#web-apis).

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## Webb-API:er
Du kan använda Azure AD B2C för att skydda webbtjänster, till exempel appens RESTful-webb-API. Webb-API:er kan använda OAuth 2.0 för att skydda sina data. De kan också autentisera inkommande HTTP-begäranden med hjälp av token. Anroparen av ett webb-API lägger till en token i auktoriseringshuvudet för en HTTP-begäran:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Webb-API:et kan sedan använda token för att verifiera API-anroparens identitet och för att extrahera information om anroparen från anspråk som är kodade i token. Mer information om vilka typer av token och anspråk som är tillgängliga för en app finns i [referens för Azure AD B2C-token](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]
    Azure AD B2C Preview stöder för närvarande endast webb-API:er som används av egna välkända klienter. Din fullständiga app kan exempelvis omfatta en iOS-app, en Android-app och ett backend-webb-API. Den här arkitekturen stöds fullt ut. För närvarande kan du inte ge en partnerklient, till exempel en annan iOS-app, åtkomst till samma webb-API. Alla komponenter i en fullständig app måste dela samma program-ID.

Ett webb-API kan ta emot token från många typer av klienter, inklusive webbappar, skrivbordsappar och mobilappar, appar med en enda sida, server-deamon och andra webb-API:er. Här är ett exempel på det fullständiga flödet för en webbapp som anropar ett webb-API:

![Bild i spaltformat som illustrerar webb-API:et för en webbapp](./media/active-directory-b2c-apps/webapi.png)

Mer information om auktoriseringskoder, uppdateringstoken och stegen för att hämta token finns i [OAuth 2.0-protokollet](active-directory-b2c-reference-oauth-code.md).

Mer information om hur du skyddar ett webb-API med hjälp av Azure AD B2C finns i självstudiekurserna om webb-API:er i [Komma igång-avsnittet](active-directory-b2c-overview.md#getting-started).

## Mobila och interna appar
Appar som installeras på enheter, till exempel mobilappar och skrivbordsappar, behöver ofta åtkomst till backend-tjänster eller webb-API:er. Du kan lägga till anpassade identitetshanteringsmiljöer i dina interna appar och på ett säkert sätt anropa backend-tjänster med hjälp av Azure AD B2C och [OAuth 2.0-auktoriseringskodflödet](active-directory-b2c-reference-oauth-code.md).  

I det här flödet kör appen [principer](active-directory-b2c-reference-policies.md) och tar emot en `authorization_code` från Azure AD när användaren uppfyller principen. `authorization_code` representerar appens behörighet att anropa backend-tjänster för den inloggade användaren. Appen kan sedan byta `authorization_code` i bakgrunden mot en `id_token` och en `refresh_token`.  Appen kan använda `id_token` för att autentisera mot ett backend-webb-API i HTTP-förfrågningar. Den kan också använda `refresh_token` för att hämta en ny `id_token` när en äldre upphör att gälla.

> [AZURE.NOTE]
    Azure AD B2C Preview stöder för närvarande endast ID-token som används för att komma åt en apps egen backend-webbtjänst. Din fullständiga app kan exempelvis omfatta en iOS-app, en Android-app och ett backend-webb-API. Den här arkitekturen stöds fullt ut. För närvarande går det inte att ge en iOS-app åtkomst till ett partnerwebb-API med hjälp av OAuth 2.0-åtkomsttoken. Alla komponenter i en fullständig app måste dela samma program-ID.

![Bild i spaltformat som illustrerar en intern app](./media/active-directory-b2c-apps/native.png)

## Aktuella begränsningar i förhandsversionen
För närvarande stöder inte Azure AD B2C Preview följande typer av appar, men stöd för dem kommer att finnas när Azure AD B2C blir offentligt tillgängligt. Övriga begränsningar och restriktioner relaterade till Azure AD B2C Preview beskrivs i [Begränsningar och restriktioner](active-directory-b2c-limitations.md).

### Appar med enstaka sidor (JavaScript)
Klientdelen i många moderna appar innehåller en app med en enda sida som främst är skriven i JavaScript. De använder ofta ett ramverk som AngularJS, Ember.js eller Durandal. Den allmänt tillgängliga Azure AD-tjänsten har stöd för dessa appar genom det implicita flödet för OAuth 2.0. Detta flöde är dock inte tillgängligt i Azure AD B2C än. Men det bör inte dröja länge.

### Daemon/appar på serversidan
Appar som innehåller tidskrävande processer eller som fungerar utan närvaron av en användare måste också kunna komma åt skyddade resurser, till exempel webb-API:er. Dessa appar kan autentisera och hämta token genom att använda appens identitet (i stället för en användares delegerade identitet) och genom att använda flödet för OAuth 2.0-klientautentiseringsuppgifter.

För närvarande stöds inte detta flöde av Azure AD B2C. De här apparna kan bara hämta token efter ett interaktivt användarflöde. Vi planerar att lägga till flödet för klientautentiseringsuppgifter inom en snar framtid.

### Webb-API-länkar (On-Behalf-Of-flöde)
Många arkitekturer har ett webb-API som måste anropa ett annat underordnat webb-API, där både skyddas av Azure AD B2C. Det här scenariot är vanligt i interna klienter som har ett webb-API på serversidan. Detta anropar sedan en Microsoft-onlinetjänst som Azure AD Graph API.

Det här scenariot med länkade webb-API:er kan användas genom en tilldelning av OAuth 2.0 JWT-ägarautentiseringsuppgifter, även kallat On-Behalf-Of-flöde.  Detta flöde är emellertid inte implementerat i Azure AD B2C Preview.



<!--HONumber=Jun16_HO2-->


