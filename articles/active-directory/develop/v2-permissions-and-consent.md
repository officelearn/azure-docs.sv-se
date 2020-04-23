---
title: Microsofts identitetsplattformsscope, behörigheter och samtycke
description: En beskrivning av auktoriseringen i slutpunkten för Microsofts identitetsplattform, inklusive scope, behörigheter och medgivande.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 1/3/2020
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: aaddev, fasttrack-edit
ms.openlocfilehash: 5495aa6fda189897985ed2f198f6e92c996f6fef
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868383"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Behörigheter och medgivande i slutpunkten för Microsoft-identitetsplattform

Program som integreras med Microsofts identitetsplattform följer en auktoriseringsmodell som ger användare och administratörer kontroll över hur data kan nås. Implementeringen av auktoriseringsmodellen har uppdaterats på slutpunkten för Microsofts identitetsplattform och ändrar hur en app måste interagera med Microsofts identitetsplattform. Den här artikeln beskriver de grundläggande begreppen i den här auktoriseringsmodellen, inklusive scope, behörigheter och medgivande.

## <a name="scopes-and-permissions"></a>Scope och behörigheter

Microsoft-identitetsplattformen implementerar Auktoriseringsprotokollet [OAuth 2.0.](active-directory-v2-protocols.md) OAuth 2.0 är en metod genom vilken en tredjepartsapp kan komma åt webbaserade resurser för en användares räkning. Alla webbaserade resurser som integreras med Microsofts identitetsplattform har en resursidentifierare eller *Application ID URI*. Några av Microsofts webbaserade resurser är till exempel:

* Microsoft Diagram:`https://graph.microsoft.com`
* Api för Office 365 E-post:`https://outlook.office.com`
* Azure Key Vault:`https://vault.azure.net`

> [!NOTE]
> Vi rekommenderar starkt att du använder Microsoft Graph i stället för Office 365 Mail API, etc.

Detsamma gäller för alla tredjepartsresurser som har integrerats med Microsofts identitetsplattform. Alla dessa resurser kan också definiera en uppsättning behörigheter som kan användas för att dela upp resursens funktioner i mindre segment. Som ett exempel har [Microsoft Graph](https://graph.microsoft.com) definierade behörigheter för att utföra följande uppgifter, bland annat:

* Läsa en användares kalender
* Skriva till en användares kalender
* Skicka e-post som användare

Genom att definiera dessa typer av behörigheter har resursen finkornig kontroll över sina data och hur API-funktioner exponeras. En app från tredje part kan begära dessa behörigheter från användare och administratörer, som måste godkänna begäran innan appen kan komma åt data eller agera för en användares räkning. Genom att segmentera resursens funktioner i mindre behörighetsgrupper kan appar från tredje part skapas för att begära endast de specifika behörigheter som de behöver för att utföra sin funktion. Användare och administratörer kan veta exakt vilka data appen har åtkomst till och de kan vara mer säkra på att den inte beter sig med ont uppsåt. Utvecklare bör alltid följa begreppet lägsta privilegium, be om endast de behörigheter de behöver för sina program att fungera.

I OAuth 2.0 kallas dessa typer av behörigheter *för scope*. De kallas också ofta *för behörigheter*. En behörighet representeras i Microsofts identitetsplattform som ett strängvärde. Om du fortsätter med Exemplet Microsoft Graph är strängvärdet för varje behörighet:

* Läsa en användares kalender med hjälp av`Calendars.Read`
* Skriv till en användares kalender med hjälp av`Calendars.ReadWrite`
* Skicka e-post som användare med hjälp av`Mail.Send`

En app begär oftast dessa behörigheter genom att ange scope i begäranden till Microsoft identity-plattformens auktorisering av slutpunkt. Vissa behörigheter med hög behörighet kan dock endast beviljas genom administratörsgodkännande och begärs/beviljas med hjälp av [slutpunkten för administratörsgodkännande](v2-permissions-and-consent.md#admin-restricted-permissions). Läs vidare för att läsa mer.

## <a name="permission-types"></a>Behörighetstyper

Microsofts identitetsplattform stöder två typer av behörigheter: **delegerade behörigheter** och **programbehörigheter**.

* **Delegerade behörigheter** används av appar som har en inloggad användare närvarande. För dessa appar samtycker antingen användaren eller en administratör till de behörigheter som appen begär, och appen delegeras behörighet att fungera som inloggad användare när du ringer till målresursen. Vissa delegerade behörigheter kan godkännas av icke-administrativa användare, men vissa behörigheter med högre behörighet kräver [administratörsgodkännande](v2-permissions-and-consent.md#admin-restricted-permissions). Information om vilka administratörsroller som kan medgivande till delegerade behörigheter finns [i Administratörsrollbehörigheter i Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* **Programbehörigheter** används av appar som körs utan en inloggad användare. till exempel appar som körs som bakgrundstjänster eller demoner.  Programbehörigheter kan endast [godkännas av en administratör](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

_Gällande behörigheter_ är de behörigheter som din app kommer att ha när du gör förfrågningar till målresursen. Det är viktigt att förstå skillnaden mellan de delegerade och programbehörigheter som din app beviljas och dess gällande behörigheter när du ringer till målresursen.

- För delegerade behörigheter är de _gällande behörigheterna_ för din app den minst privilegierade skärningspunkten mellan de delegerade behörigheter som appen har beviljats (via medgivande) och behörigheterna för den inloggade användaren. Din app kan aldrig ha fler behörigheter än den inloggade användaren. Inom organisationer kan behörigheter för den inloggade användaren fastställas med en princip eller av medlemskap i en eller flera administratörsroller. Information om vilka administratörsroller som kan medgivande till delegerade behörigheter finns [i Administratörsrollbehörigheter i Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

   Anta till exempel att appen har tilldelats _behörigheten User.ReadWrite.All._ Den här behörigheten ger i princip din app behörighet att läsa och uppdatera profilen för alla användare i en organisation. Om den inloggade användaren är en global administratör, kommer din app att kunna uppdatera profilen för alla användare i organisationen. Men om den inloggade användaren inte är i en administratörsroll kan appen bara uppdatera profilen för den inloggade användaren. Den kommer inte att kunna uppdatera profilerna för andra användare i organisationen, eftersom den användare som den har behörighet att agera på uppdrag åt inte har den behörigheten.

- För programbehörigheter är de _gällande behörigheterna_ för din app den fulla behörighetsnivån som följer av behörigheten. En app som har programbehörigheten _User.ReadWrite.All_ kan till exempel uppdatera profilen för alla användare i organisationen.

## <a name="openid-connect-scopes"></a>OpenID Connect-scope

Implementeringen av Microsoft-identitetsplattformen openID Connect har några väldefinierade scope `openid` `email`som `profile`inte `offline_access`gäller för en viss resurs: , , och . Och `address` `phone` OpenID Connect-scope stöds inte.

### <a name="openid"></a>Openid

Om en app utför inloggning med [OpenID Connect](active-directory-v2-protocols.md)måste `openid` den begära omfånget. Scopet `openid` visas på sidan med samtycke till arbetskonto som behörigheten "Logga in dig" och på den personliga microsoft-kontomedgivandesidan som behörigheten "Visa din profil och anslut till appar och tjänster med ditt Microsoft-konto". Med den här behörigheten kan en app få en unik `sub` identifierare för användaren i form av anspråket. Det ger också appen åtkomst till UserInfo-slutpunkten. Scopet `openid` kan användas på Microsoft identity platform token slutpunkten för att hämta ID-token, som kan användas av appen för autentisering.

### <a name="email"></a>e-post

Scopet `email` kan användas `openid` med omfånget och alla andra. Det ger appen åtkomst till användarens primära e-postadress `email` i form av anspråket. Anspråket `email` ingår bara i en token om en e-postadress är kopplad till användarkontot, vilket inte alltid är fallet. Om appen `email` används bör den vara beredd att hantera `email` ett fall där anspråket inte finns i token.

### <a name="profile"></a>profil

Scopet `profile` kan användas `openid` med omfånget och alla andra. Det ger appen tillgång till en betydande mängd information om användaren. Informationen som den kan komma åt innehåller, men är inte begränsad till, användarens förnamn, efternamn, önskat användarnamn och objekt-ID. En fullständig lista över de profilanspråk som är tillgängliga i parametern id_tokens för en viss användare finns i [ `id_tokens` referensen](id-tokens.md).

### <a name="offline_access"></a>offline_access

[ `offline_access` Omfattningen](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) ger din app åtkomst till resurser för användarens räkning under en längre tid. På medgivandesidan visas det här scopet som behörigheten "Underhåll åtkomst till data som du har gett den åtkomst till". När en användare `offline_access` godkänner scopet kan appen ta emot uppdateringstoken från slutpunkten för Microsoft identity platform token. Uppdateringstoken är långlivade. Din app kan få nya åtkomsttoken när äldre förfaller.

> [!NOTE]
> Den här behörigheten visas på alla medgivandeskärmar idag, även för flöden som inte tillhandahåller en uppdateringstoken (det [implicita flödet).](v2-oauth2-implicit-grant-flow.md)  Detta för att täcka scenarier där en klient kan börja inom det implicita flödet och sedan gå vidare till kodflödet där en uppdateringstoken förväntas.

På Microsofts identitetsplattform (begäranden till v2.0-slutpunkten) `offline_access` måste appen uttryckligen begära omfånget för att ta emot uppdateringstoken. Det innebär att när du löser in en auktoriseringskod i [OAuth 2.0-auktoriseringskodflödet](active-directory-v2-protocols.md)får du bara en åtkomsttoken från `/token` slutpunkten. Åtkomsttoken är giltig en kort stund. Åtkomsttoken upphör vanligtvis att gälla om en timme. Då måste appen omdirigera användaren tillbaka till `/authorize` slutpunkten för att få en ny auktoriseringskod. Under den här omdirigeringen, beroende på vilken typ av app det är, kan användaren behöva ange sina autentiseringsuppgifter igen eller godkänna behörigheter igen.

Mer information om hur du hämtar och använder uppdateringstoken finns i [microsoft identity platform protocol reference](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Begära individuellt användarmedgivande

I en [OpenID Connect- eller OAuth 2.0-auktoriseringsbegäran](active-directory-v2-protocols.md) `scope` kan en app begära de behörigheter den behöver med hjälp av frågeparametern. När en användare till exempel loggar in på en app skickar appen en begäran som följande exempel (med radbrytningar tillagda för läsbarhet):

```HTTP
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&response_type=code
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&response_mode=query
&scope=
https%3A%2F%2Fgraph.microsoft.com%2Fcalendars.read%20
https%3A%2F%2Fgraph.microsoft.com%2Fmail.send
&state=12345
```

Parametern `scope` är en utrymmesavgränsad lista över delegerade behörigheter som appen begär. Varje behörighet anges genom att lägga till behörighetsvärdet till resursens identifierare (Application ID URI). I exemplet med begäran måste appen ha behörighet att läsa användarens kalender och skicka e-post som användare.

När användaren har angett sina autentiseringsuppgifter söker slutpunkten för Microsoft identity platform efter en matchande post för *användarens medgivande*. Om användaren inte har samtyckt till någon av de begärda behörigheterna tidigare, och inte heller har en administratör medgivande till dessa behörigheter för hela organisationen, ber Slutpunkten för Microsoft-identitetsplattformen användaren att bevilja de begärda behörigheterna.

> [!NOTE]
>För närvarande inkluderas `offline_access` ("Behåll åtkomst till data som `user.read` du har gett den åtkomst till") och ("Logga in dig och läs din profil") automatiskt i det ursprungliga medgivandet till ett program.  Dessa behörigheter krävs i allmänhet för `offline_access` korrekt appfunktionalitet – ger appen åtkomst till `user.read` uppdateringstoken, som är viktiga för inbyggda appar och webbappar, samtidigt som de ger åtkomst till anspråket, `sub` vilket gör att klienten eller appen kan identifiera användaren korrekt över tid och komma åt rudimentär användarinformation.

![Exempel skärmdump som visar arbetskonto samtycke](./media/v2-permissions-and-consent/work_account_consent.png)

När användaren godkänner behörighetsbegäran registreras samtycke och användaren behöver inte godkänna igen vid efterföljande inloggningar till programmet.

## <a name="requesting-consent-for-an-entire-tenant"></a>Begära samtycke för en hel klient

När en organisation köper en licens eller prenumeration för ett program vill organisationen ofta proaktivt konfigurera programmet för användning av alla medlemmar i organisationen. Som en del av den här processen kan en administratör bevilja samtycke för att programmet ska agera på uppdrag av alla användare i klienten. Om administratören ger medgivande för hela klienten visas inte en medgivandesida för programmet för organisationen.

Om du vill begära medgivande för delegerade behörigheter för alla användare i en klient kan appen använda slutpunkten för administratörsmedgivande.

Dessutom måste program använda slutpunkten för administratörsgodkännande för att begära programbehörigheter.

## <a name="admin-restricted-permissions"></a>Behörigheter med administratörsbegränsad

Vissa behörigheter med hög behörighet i Microsofts ekosystem kan ställas in *på administratörsbegränsad*. Exempel på dessa typer av behörigheter är följande:

* Läs alla användares fullständiga profiler med hjälp av`User.Read.All`
* Skriva data till en organisations katalog med hjälp av`Directory.ReadWrite.All`
* Läs alla grupper i en organisations katalog med hjälp av`Groups.Read.All`

Även om en konsumentanvändare kan ge ett program åtkomst till den här typen av data, är organisationsanvändare begränsade från att bevilja åtkomst till samma uppsättning känsliga företagsdata. Om ditt program begär åtkomst till någon av dessa behörigheter från en organisationsanvändare får användaren ett felmeddelande som säger att de inte har behörighet att godkänna appens behörigheter.

Om din app kräver åtkomst till administratörsbegränsade scope för organisationer bör du begära dem direkt från en företagsadministratör, även med hjälp av slutpunkten för administratörsmedgivande, som beskrivs nedan.

Om programmet begär delegerade behörigheter med hög behörighet och en administratör beviljar dessa behörigheter via slutpunkten för administratörsgodkännande beviljas medgivande för alla användare i klienten.

Om programmet begär programbehörigheter och en administratör beviljar dessa behörigheter via slutpunkten för administratörsgodkännande, görs inte det här bidraget för någon specifik användare. Klientprogrammet beviljas *i*stället behörighet direkt . Dessa typer av behörigheter används endast av daemontjänster och andra icke-interaktiva program som körs i bakgrunden.

## <a name="using-the-admin-consent-endpoint"></a>Använda slutpunkten för administratörsmedgivande

> [!NOTE]
> Observera att efter att ha beviljat administratörsgodkännande med hjälp av administratörsmedgivandeslutpunkten har du slutfört beviljandet av administratörsmedgivande och användare behöver inte utföra några ytterligare åtgärder. När du har beviljat administratörsmedgivande kan användare få en åtkomsttoken via ett typiskt auth-flöde och den resulterande åtkomsttoken har behörigheten med medgivande.

När en företagsadministratör använder ditt program och dirigeras till den auktorisera slutpunkten identifierar Microsofts identitetsplattform användarens roll och frågar dem om de vill godkänna för hela klientens räkning om de behörigheter du har begärt. Det finns dock också en dedikerad slutpunkt för administratörsgodkännande som du kan använda om du vill proaktivt begära att en administratör beviljar behörighet för hela klienten. Det är också nödvändigt att använda den här slutpunkten för att begära programbehörigheter (som inte kan begäras med hjälp av den auktoriserade slutpunkten).

Om du följer dessa steg kan appen begära behörigheter för alla användare i en klient, inklusive administratörsbegränsade scope. Detta är en hög behörighetsåtgärd och bör endast göras om det behövs för ditt scenario.

Ett kodexempel som implementerar stegen finns i [exemplet med administratörsbegränsade scope](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Begär behörigheterna i appregistreringsportalen

Program kan notera vilka behörigheter de behöver (både delegerade och program) i appregistreringsportalen.  Detta gör det `/.default` möjligt att använda scopet och Azure-portalens alternativet "Bevilja administratörsgodkännande".  I allmänhet är det bäst att se till att de behörigheter som statiskt definierats för ett visst program är en superuppsättning av de behörigheter som den kommer att begära dynamiskt/stegvis.

> [!NOTE]
>Programbehörigheter kan endast begäras [`/.default`](#the-default-scope) med hjälp av - så om din app behöver programbehörigheter, se till att de visas i appregistreringsportalen.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Så här konfigurerar du listan över statiskt begärda behörigheter för ett program

1. Gå till ditt program i [Azure-portalen – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) eller [skapa en app](quickstart-register-app.md) om du inte redan har gjort det.
2. Leta reda på avsnittet **API-behörigheter** och klicka på Lägg till en behörighet i API-behörigheterna.
3. Välj **Microsoft Graph** i listan över tillgängliga API:er och lägg sedan till de behörigheter som appen kräver.
3. **Spara** appregistreringen.

### <a name="recommended-sign-the-user-into-your-app"></a>Rekommenderas: Logga in användaren i appen

När du skapar ett program som använder slutpunkten för administratörsmedgivande behöver appen vanligtvis en sida eller vy där administratören kan godkänna appens behörigheter. Den här sidan kan vara en del av appens registreringsflöde, en del av appens inställningar, eller så kan det vara ett dedikerat "connect"-flöde. I många fall är det vettigt att appen visar den här "connect"-vyn först när en användare har loggat in med ett Microsoft-konto för arbete eller skola.

När du signerar användaren i din app kan du identifiera den organisation som administratören tillhör innan du ber dem att godkänna de behörigheter som krävs. Även om det inte är absolut nödvändigt kan det hjälpa dig att skapa en mer intuitiv upplevelse för dina organisationsanvändare. Om du vill logga in användaren följer du våra [självstudier för Microsoft Identity Platform Protocol](active-directory-v2-protocols.md).

### <a name="request-the-permissions-from-a-directory-admin"></a>Begär behörigheter från en katalogadministratör

När du är redo att begära behörigheter från organisationens administratör kan du omdirigera användaren till *slutpunkten för administratör*för Microsoft-identitetsplattform.

```HTTP
// Line breaks are for legibility only.
GET https://login.microsoftonline.com/{tenant}/v2.0/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
&scope=
https://graph.microsoft.com/calendars.read
https://graph.microsoft.com/mail.send
```


| Parameter        | Villkor        | Beskrivning                                                                                |
|:--------------|:--------------|:-----------------------------------------------------------------------------------------|
| `tenant` | Krävs | Den katalogklient som du vill begära behörighet från. Kan tillhandahållas i GUID eller eget namnformat ELLER allmänt refererat till organisationer som visas i exemplet. Använd inte "vanligt", eftersom personliga konton inte kan ge administratörsgodkännande utom i samband med en klient. Använd klient-ID när det är möjligt för att säkerställa bästa kompatibilitet med personliga konton som hanterar klienter. |
| `client_id` | Krävs | **Program-ID (klient)** som [Azure-portalen – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) erfarenhet som tilldelats din app. |
| `redirect_uri` | Krävs |Omdirigerings-URI:n där du vill att svaret ska skickas för att appen ska hanteras. Den måste exakt matcha en av de omdirigerings-URI:er som du registrerade i appregistreringsportalen. |
| `state` | Rekommenderas | Ett värde som ingår i begäran som också returneras i tokensvaret. Det kan vara en sträng av vilket innehåll du vill. Använd tillståndet för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel sidan eller vyn som de var på. |
|`scope`        | Krävs        | Definierar den uppsättning behörigheter som begärs av programmet. Detta kan vara antingen [`/.default`](#the-default-scope)statiska (med ) eller dynamiska scope.  Detta kan inkludera OIDC-scope `profile` `email`(`openid`, , ). Om du behöver programbehörigheter `/.default` måste du använda för att begära den statiskt konfigurerade listan med behörigheter.  |


Nu kräver Azure AD att en klientadministratör loggar in för att slutföra begäran. Administratören uppmanas att godkänna alla behörigheter som `scope` du har begärt i parametern.  Om du har använt`/.default`ett statiskt ( ) värde fungerar det som slutpunkten v1.0 admin consent och begär medgivande för alla scope som finns i de behörigheter som krävs för appen.

#### <a name="successful-response"></a>Lyckat svar

Om administratören godkänner behörigheterna för din app ser det lyckade svaret ut så här:

```HTTP
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beskrivning |
| --- | --- |
| `tenant` | Katalogklienten som gav ditt program de behörigheter som begärs, i GUID-format. |
| `state` | Ett värde som ingår i begäran som också kommer att returneras i tokensvaret. Det kan vara en sträng av vilket innehåll du vill. Tillståndet används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, till exempel sidan eller vyn som de var på. |
| `admin_consent` | Kommer att `True`ställas in på . |

#### <a name="error-response"></a>Felsvar

Om administratören inte godkänner behörigheterna för din app ser det misslyckade svaret ut så här:

```HTTP
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beskrivning |
| --- | --- |
| `error` | En felkodsträng som kan användas för att klassificera typer av fel som uppstår och som kan användas för att reagera på fel. |
| `error_description` | Ett specifikt felmeddelande som kan hjälpa en utvecklare att identifiera orsaken till ett fel. |

När du har fått ett lyckat svar från slutpunkten för administratörsmedgivande har appen fått de behörigheter som begärs. Därefter kan du begära en token för den resurs du vill ha.

## <a name="using-permissions"></a>Använda behörigheter

När användaren har medgivandet till behörigheter för din app kan appen hämta åtkomsttoken som representerar appens behörighet att komma åt en resurs i viss kapacitet. En åtkomsttoken kan endast användas för en enskild resurs, men kodad inuti åtkomsttoken är all behörighet som din app har beviljats för den resursen. Om du vill hämta en åtkomsttoken kan appen göra en begäran till slutpunkten för Microsoft identity platform-token, så här:

```HTTP
POST common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/json

{
    "grant_type": "authorization_code",
    "client_id": "6731de76-14a6-49ae-97bc-6eba6914391e",
    "scope": "https://outlook.office.com/mail.read https://outlook.office.com/mail.send",
    "code": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq..."
    "redirect_uri": "https://localhost/myapp",
    "client_secret": "zc53fwe80980293klaj9823"  // NOTE: Only required for web apps
}
```

Du kan använda den resulterande åtkomsttoken i HTTP-begäranden till resursen. Det visar på ett tillförlitligt sätt för resursen att appen har rätt behörighet att utföra en viss uppgift.

Mer information om OAuth 2.0-protokollet och hur du får åtkomsttoken finns i [microsoft identity platform endpoint protocol reference](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>Standardomfånget /.default

Du kan `/.default` använda scopet för att migrera dina appar från v1.0-slutpunkten till slutpunkten för Microsoft-identitetsplattformen. Detta är ett inbyggt scope för alla program som refererar till den statiska listan över behörigheter som konfigurerats för programregistreringen. Ett `scope` värde `https://graph.microsoft.com/.default` av är funktionellt samma som v1.0-slutpunkterna `resource=https://graph.microsoft.com` - det vill säga begär en token med scope på Microsoft Graph som programmet har registrerats för i Azure-portalen.  Den konstrueras med hjälp `/.default` av resursen URI + (t.ex. om resursen URI är `https://contosoApp.com`, då det begärda scopet skulle vara). `https://contosoApp.com/.default`  Se [avsnittet om avslutande snedstreck](#trailing-slash-and-default) för fall där du måste inkludera ett andra snedstreck för att korrekt begära token.

/.default-scopet kan användas i alla OAuth 2.0-flöde, men är nödvändigt i [flödet On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) och [klientautentiseringsflödet](v2-oauth2-client-creds-grant-flow.md)samt när du använder slutpunkten v2-administratörsgodkännande för att begära programbehörigheter.

> [!NOTE]
> Klienter kan inte kombinera`/.default`statisk ( ) och dynamiskt medgivande i en enda begäran. Således `scope=https://graph.microsoft.com/.default+mail.read` kommer att resultera i ett fel på grund av kombinationen av omfattning typer.

### <a name="default-and-consent"></a>/.standard och samtycke

Scopet `/.default` utlöser v1.0-slutpunktsbeteendet för `prompt=consent` också. Den begär samtycke för alla behörigheter som registrerats av programmet, oavsett resursen. Om `/.default` den ingår som en del av begäran returnerar scopet en token som innehåller scope för den begärda resursen.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default när användaren redan har gett sitt samtycke

Eftersom `/.default` är funktionellt `resource`identisk med -centrerad v1.0-slutpunktens beteende, för det med sig samtyckesbeteendet för v1.0-slutpunkten också. Nämligen `/.default` utlöser bara en samtyckesfråga om ingen behörighet har beviljats mellan klienten och resursen av användaren. Om det finns något sådant medgivande returneras en token som innehåller alla scope som beviljas av användaren för den resursen. Om ingen behörighet har beviljats, `prompt=consent` eller om parametern har tillhandahållits, visas en medgivandefråga för alla scope som registrerats av klientprogrammet.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Exempel 1: Användaren, eller klientadministratören, har beviljat behörigheter

I det här exemplet har användaren (eller en klientadministratör) `mail.read` beviljat `user.read`klienten behörigheterna för Microsoft Graph och . Om klienten gör `scope=https://graph.microsoft.com/.default`en begäran om visas ingen samtyckesfråga oavsett innehållet i klientprogrammens registrerade behörigheter för Microsoft Graph. En token skulle returneras som `mail.read` `user.read`innehåller scope och .

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Exempel 2: Användaren har inte beviljat behörigheter mellan klienten och resursen

I det här exemplet finns inget samtycke för användaren mellan klienten och Microsoft Graph. Klienten har registrerat `user.read` `contacts.read` sig för och behörigheter, samt `https://vault.azure.net/user_impersonation`Azure Key Vault-scope . När klienten begär `scope=https://graph.microsoft.com/.default`en token för visas en `user.read`medgivandeskärm för scopea för , `contacts.read`och Key Vault. `user_impersonation` Token som returneras har `user.read` `contacts.read` bara och omfattningar i den och endast kan användas mot Microsoft Graph.

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Exempel 3: Användaren har samtyckt och klienten begär ytterligare scope

I det här exemplet har användaren `mail.read` redan samtyckt till för klienten. Klienten har registrerat `contacts.read` sig för omfattningen i registreringen. När klienten gör en begäran `scope=https://graph.microsoft.com/.default` om en `prompt=consent`token med hjälp av och begär samtycke via , kommer användaren att se en medgivandeskärm för alla (och endast) de behörigheter som registrerats av programmet. `contacts.read`kommer att finnas på samtyckesskärmen, men `mail.read` kommer inte att göra det. Den returnerade token är för Microsoft `mail.read` `contacts.read`Graph och kommer att innehålla och .

### <a name="using-the-default-scope-with-the-client"></a>Använda standardomfånget /.default med klienten

Ett specialfall `/.default` av omfånget finns `/.default` när en klient begär sitt eget scope. Följande exempel visar det här scenariot.

```HTTP
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Detta ger en medgivandeskärm för alla registrerade behörigheter (om `/.default`tillämpligt baserat på ovanstående beskrivningar av samtycke och ), returnerar sedan en id_token i stället för en åtkomsttoken.  Det här problemet finns för vissa äldre klienter som flyttar från ADAL till MSAL och **bör inte** användas av nya klienter som är inriktade på slutpunkten för Microsoft-identitetsplattformen.

### <a name="trailing-slash-and-default"></a>Avslutande snedstreck och /.default

Vissa resurs-URI:er`https://contoso.com/` har ett `https://contoso.com`avslutande snedstreck ( i motsats till ), vilket kan orsaka problem med tokenvalidering.  Detta kan främst inträffa när du begär`https://management.azure.com/`en token för Azure Resource Management ( ), som har ett avslutande snedstreck på sin resurs URI och kräver att den finns när token begärs.  Således, när du begär `https://management.azure.com/` en `/.default`token för `https://management.azure.com//.default` och använda, måste du begära - notera dubbel snedstreck!

I allmänhet - om du har validerat att token utfärdas och token avvisas av API:et som ska acceptera det, överväg att lägga till ett andra snedstreck och försöka igen. Detta beror på att inloggningsservern avger en token `scope` med `/.default` målgruppen som matchar URI:erna i parametern - med borttaget från slutet.  Om detta tar bort det efterföljande snedstrecket bearbetar inloggningsservern fortfarande begäran och validerar den mot resursen URI, även om de inte längre matchar - detta är icke-standardiserat och bör inte åberopas av ditt program.

## <a name="troubleshooting-permissions-and-consent"></a>Felsöka behörigheter och medgivande

Om du eller programmets användare ser oväntade fel under medgivandeprocessen läser du den här artikeln för felsökningssteg: [Oväntat fel när du utför medgivande till ett program](../manage-apps/application-sign-in-unexpected-user-consent-error.md).
