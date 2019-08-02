---
title: Microsoft Identity Platform-omfattningar, behörigheter och medgivande | Microsoft Docs
description: En beskrivning av auktorisering i slut punkten för Microsoft Identity Platform, inklusive omfång, behörigheter och medgivande.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 8f98cbf0-a71d-4e34-babf-e644ad9ff423
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin, jesakowi, jmprieur
ms.custom: fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c0fcb748262b20fd4550d08d74056c0219dbc09
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2019
ms.locfileid: "68694009"
---
# <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Behörigheter och medgivande i Microsoft Identity Platform-slutpunkten

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Program som integreras med Microsoft Identity Platform följer en auktoriserings modell som ger användare och administratörer kontroll över hur data kan nås. Implementeringen av auktoriserings modellen har uppdaterats på Microsoft Identity Platform-slutpunkten och den ändrar hur en app måste interagera med Microsoft Identity Platform. Den här artikeln beskriver de grundläggande begreppen i den här verifierings modellen, inklusive omfång, behörigheter och medgivande.

> [!NOTE]
> Slut punkten för Microsoft Identity Platform stöder inte alla scenarier och funktioner. För att avgöra om du ska använda Microsoft Identity Platform-slutpunkten läser du om [begränsningar för Microsoft Identity Platform](active-directory-v2-limitations.md).

## <a name="scopes-and-permissions"></a>Omfattningar och behörigheter

Microsoft Identity Platform implementerar [OAuth 2,0](active-directory-v2-protocols.md) -Authorization-protokollet. OAuth 2,0 är en metod som innebär att en app från tredje part kan komma åt webb värd resurser för en användares räkning. Alla webb värd resurser som integreras med Microsoft Identity Platform har en resurs identifierare eller en URI för *program-ID*. Till exempel är några av Microsofts webb värd resurser:

* Microsoft Graph: `https://graph.microsoft.com`
* E-post-API för Office 365:`https://outlook.office.com`
* Azure AD-diagram:`https://graph.windows.net`

> [!NOTE]
> Vi rekommenderar starkt att du använder Microsoft Graph i stället för Azure AD Graph, Office 365 Mail API osv.

Samma sak gäller för alla resurser från tredje part som har integrerats med Microsoft Identity Platform. Alla dessa resurser kan också definiera en uppsättning behörigheter som kan användas för att dela upp resursens funktioner i mindre segment. [Microsoft Graph](https://graph.microsoft.com) har exempelvis definierat behörigheter för att utföra följande uppgifter, bland annat:

* Läs en användares kalender
* Skriv till en användares kalender
* Skicka e-post som en användare

Genom att definiera dessa typer av behörigheter har resursen detaljerad kontroll över sina data och hur API-funktioner exponeras. En app från tredje part kan begära dessa behörigheter från användare och administratörer, som måste godkänna begäran innan appen kan komma åt data eller agera för användarens räkning. Genom att dela upp resursens funktioner i mindre behörighets uppsättningar kan appar från tredje part byggas för att begära enbart de angivna behörigheter som de behöver för att utföra sin funktion. Användare och administratörer kan veta exakt vilka data som appen har åtkomst till, och de kan vara mer säkra på att de inte fungerar med skadlig avsikt. Utvecklare bör alltid följa konceptet med minsta behörighet och endast begära de behörigheter de behöver för att deras program ska fungera.

I OAuth 2,0 kallas dessa typer av behörigheter för *omfattningar*. De kallas också ofta för *behörigheter*. En behörighet visas i Microsoft Identity Platform som ett sträng värde. Om du fortsätter med Microsoft Graph exemplet är strängvärdet för varje behörighet:

* Läs en användares kalender genom att använda`Calendars.Read`
* Skriv till en användares kalender genom att använda`Calendars.ReadWrite`
* Skicka e-post som en användare med hjälp av`Mail.Send`

En app begär oftast dessa behörigheter genom att ange omfattningarna i begär anden till Microsoft Identity Platform permission-slutpunkten. Vissa behörigheter för hög behörighet kan dock endast beviljas genom administratörs medgivande och begärs/beviljas med hjälp av [Administratörs medgivande slut punkten](v2-permissions-and-consent.md#admin-restricted-permissions). Fortsätt att läsa om du vill veta mer.

## <a name="permission-types"></a>Behörighets typer

Microsoft Identity Platform stöder två typer av behörigheter: **delegerade behörigheter** och **program behörigheter**.

* **Delegerade behörigheter** används av appar där en inloggad användare finns. För dessa appar skickas användaren eller administratören till de behörigheter som appen begär, och appen är delegerad behörighet att fungera som den inloggade användaren när den gör anrop till mål resursen. Vissa delegerade behörigheter kan skickas till av icke-administratörer, men vissa högre privilegier kräver [Administratörs medgivande](v2-permissions-and-consent.md#admin-restricted-permissions). Information om vilka administratörs roller som kan godkänna delegerade behörigheter finns i [Administratörs roll behörigheter i Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

* **Program behörigheter** används av appar som körs utan att en inloggad användare finns. till exempel appar som körs som bakgrunds tjänster eller daemon.  Program behörigheter kan bara godkännas [av en administratör](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

_Gällande behörigheter_ är de behörigheter som appen kommer att ha när de gör förfrågningar till mål resursen. Det är viktigt att förstå skillnaden mellan de delegerade och program behörigheter som din app beviljas och dess gällande behörigheter när du gör anrop till mål resursen.

- För delegerade behörigheter är den _effektiva behörigheten_ för din app den minst privilegierade skärnings punkten för de delegerade behörigheterna som appen har beviljats (via medgivande) och behörigheten för den för tillfället inloggade användaren. Din app kan aldrig ha fler behörigheter än den inloggade användaren. Inom organisationer kan behörigheter för den inloggade användaren fastställas med en princip eller av medlemskap i en eller flera administratörsroller. Information om vilka administratörs roller som kan godkänna delegerade behörigheter finns i [Administratörs roll behörigheter i Azure AD](../users-groups-roles/directory-assign-admin-roles.md).

   Anta till exempel att din app har beviljats _User. readwrite. all_ delegerad behörighet. Den här behörigheten ger i princip din app behörighet att läsa och uppdatera profilen för alla användare i en organisation. Om den inloggade användaren är en global administratör, kommer din app att kunna uppdatera profilen för alla användare i organisationen. Men om den inloggade användaren inte har någon administratörs roll kan appen bara uppdatera profilen för den inloggade användaren. Den kommer inte att kunna uppdatera profilerna för andra användare i organisationen, eftersom den användare som den har behörighet att agera på uppdrag åt inte har den behörigheten.
  
- För program behörigheter är den _effektiva behörigheten_ för appen den fullständiga behörighets nivån som anges av behörigheten. Till exempel kan en app som har behörigheten _User. readwrite. all_ program behörighet uppdatera profilen för alla användare i organisationen. 

## <a name="openid-connect-scopes"></a>OpenID Connect-omfång

Microsoft Identity Platform-implementeringen av OpenID Connect har några väldefinierade omfattningar som inte gäller för en viss `openid`resurs: `profile`, `email`, och `offline_access`. `address` OpenIDConnect`phone` -scope stöds inte.

### <a name="openid"></a>OpenID

Om en app utför inloggning med hjälp av [OpenID Connect](active-directory-v2-protocols.md)måste den begära `openid` omfånget. `openid` Omfånget visas på sidan för godkännande av arbets konto som "logga in dig" och på sidan personal Microsoft-konto medgivande som "Visa din profil och Anslut till appar och tjänster med hjälp av din Microsoft-konto"-behörighet. Med den här behörigheten kan en app ta emot en unik identifierare för användaren i form av `sub` anspråket. Den ger också appen åtkomst till UserInfo-slutpunkten. `openid` Omfånget kan användas på Microsoft Identity Platform token-slutpunkten för att förvärva ID-token som kan användas av appen för autentisering.

### <a name="email"></a>email

Omfånget kan användas `openid` med omfattningen och andra. `email` Den ger appen åtkomst till användarens primära e-postadress i form av `email` anspråket. `email` Anspråket ingår bara i en token om en e-postadress är associerad med användar kontot, vilket inte alltid är fallet. Om den använder `email` omfånget bör din app förberedas för att hantera ett fall `email` där anspråket inte finns i token.

### <a name="profile"></a>profile

Omfånget kan användas `openid` med omfattningen och andra. `profile` Den ger appen åtkomst till en stor mängd information om användaren. Den information som den kan komma åt inkluderar, men är inte begränsad till, användarens förnamn, efter namn, önskat användar namn och objekt-ID. En fullständig lista över de profil anspråk som är tillgängliga i id_tokens-parametern för en speciell användare finns [ `id_tokens` ](id-tokens.md)i referensen.

### <a name="offlineaccess"></a>offline_access

Omfånget ger din app åtkomst till resurser för användarens räkning under en längre tid. [ `offline_access` ](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) På sidan medgivande visas denna omfattning som "Behåll åtkomst till data som du har gett dem behörighet till". När en användare godkänner `offline_access` omfånget kan din app ta emot uppdateringstoken från slut punkten för Microsoft Identity Platform-token. Uppdateringstoken är lång. Din app kan hämta nya åtkomsttoken när äldre upphör att gälla.

Om din app inte uttryckligen begär `offline_access` omfånget får den inte några uppdaterade tokens. Det innebär att när du löser in en auktoriseringskod i [OAuth 2,0-Authorization Code Flow](active-directory-v2-protocols.md)får du bara en åtkomsttoken från `/token` slut punkten. Åtkomsttoken är giltig under en kort tid. Åtkomsttoken går vanligt vis ut om en timme. Vid det här tillfället måste appen omdirigera användaren tillbaka till `/authorize` slut punkten för att få en ny auktoriseringskod. Under omdirigeringen, beroende på typ av app, kan användaren behöva ange sina autentiseringsuppgifter igen eller ge tillstånd igen till behörigheter. Även om `offline_access` scopet begärs automatiskt av servern måste klienten fortfarande begära det för att kunna ta emot uppdateringstoken.

Mer information om hur du hämtar och använder uppdaterade token finns i referens för [Microsoft Identity Platform-protokollet](active-directory-v2-protocols.md).

## <a name="requesting-individual-user-consent"></a>Begära individuell användar medgivande

I en [OpenID Connect-eller OAuth 2,0](active-directory-v2-protocols.md) -auktoriseringsbegäran kan en app begära de behörigheter som krävs med hjälp `scope` av Frågeparametern. Till exempel, när en användare loggar in i en app, skickar appen en begäran som följande exempel (med rad brytningar som har lagts till för läsbarhet):

```
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

`scope` Parametern är en blankstegsavgränsad lista med delegerade behörigheter som appen begär. Varje behörighet anges genom att lägga till behörighet svärdet till resursens identifierare (program-ID-URI). I förfrågan-exemplet måste appen ha behörighet att läsa användarens kalender och skicka e-post som användare.

När användaren har angett sina autentiseringsuppgifter söker Microsoft Identity Platform-slutpunkten efter en matchande post för *användar medgivande*. Om användaren inte har samtyckt till någon av de begärda behörigheterna tidigare, eller om en administratör har samtyckt till dessa behörigheter åt hela organisationen, ber Microsoft Identity Platform-slutpunkten användaren att bevilja de begärda behörigheterna.

> [!NOTE]
> För närvarande inkluderas behörigheterna `user.read` ("Behållåtkomsttilldatasomduharfåttåtkomsttill")och("loggainochläsadinprofil")automatisktidetförstamedgivandeettillettprogram.`offline_access`  De här behörigheterna krävs vanligt vis för korrekt app `offline_access` -funktionalitet – ger appen åtkomst till att uppdatera tokens, kritiskt för ursprungliga appar och `user.read` webbappar, samtidigt `sub` som den ger åtkomst till anspråket, vilket gör att klienten eller appen fungerar korrekt identifiera användaren över tid och komma åt elementära användar information.  

![Exempel skärm bild som visar godkännande av arbets konto](./media/v2-permissions-and-consent/work_account_consent.png)

När användaren godkänner behörighets förfrågningen registreras medgivande och användaren behöver inte godkänna det igen på efterföljande inloggningar till programmet.

## <a name="requesting-consent-for-an-entire-tenant"></a>Begära medgivande för en hel klient

När en organisation köper en licens eller prenumeration för ett program vill organisationen ofta konfigurera programmet för användning av alla medlemmar i organisationen. Som en del av den här processen kan en administratör bevilja medgivande för programmet att agera åt alla användare i klient organisationen. Om administratören ger godkännande för hela klient organisationen ser inte organisationens användare en godkännande sida för programmet.

För att begära medgivande för delegerade behörigheter för alla användare i en klient organisation kan din app använda slut punkten för administratörs medgivande.

Dessutom måste program använda den administrativa medgivande slut punkten för att begära program behörigheter.

## <a name="admin-restricted-permissions"></a>Administratör – begränsade behörigheter

Vissa behörigheter med hög behörighet i Microsoft-eko systemet kan anges till *admin-begränsad*. Exempel på dessa typer av behörigheter är följande:

* Läs alla användares fullständiga profiler med hjälp av`User.Read.All`
* Skriva data till en organisations katalog med hjälp av`Directory.ReadWrite.All`
* Läs alla grupper i en organisations katalog med hjälp av`Groups.Read.All`

Även om en konsument användare kan ge en program åtkomst till den här typen av data, begränsas organisationens användare från att bevilja åtkomst till samma uppsättning känsliga företags data. Om programmet begär åtkomst till någon av de här behörigheterna från en organisations användare får användaren ett fel meddelande om att de inte har behörighet att godkänna appens behörigheter.

Om din app kräver åtkomst till administrations begränsade omfång för organisationer, bör du begära dem direkt från en företags administratör, även genom att använda slut punkten för administratörs medgivande, som beskrivs härnäst.

Om programmet begär hög behörighet delegerade behörigheter och en administratör beviljar behörigheterna via slut punkten för administratörs medgivande beviljas medgivande för alla användare i klient organisationen.

Om programmet begär program behörigheter och en administratör beviljar behörigheterna via slut punkten för administratörs medgivande, utförs inte detta anslag för någon specifik användares räkning. I stället beviljas klient programmet behörigheter *direkt*. Dessa typer av behörigheter används endast av daemon-tjänster och andra icke-interaktiva program som körs i bakgrunden.

## <a name="using-the-admin-consent-endpoint"></a>Använda slut punkten för administratörs medgivande

> [!NOTE] 
> Observera att när du har beviljat administratörs medgivande med hjälp av administratörs slut punkten har du beviljat godkännandet och användarna behöver inte utföra några ytterligare åtgärder. Efter att ha beviljat administratörs tillåtelse kan användare få en åtkomsttoken via ett typiskt auth-flöde och den resulterande åtkomsttoken har de behörigheter som har skickats. 

När en företags administratör använder ditt program och dirigeras till behörighets slut punkten identifierar Microsoft Identity Platform användarens roll och ber dem om de vill ge tillstånd åt hela klient organisationen för de behörigheter som du har begärt. Det finns dock en dedikerad slut punkt för administratörs medgivande som du kan använda om du vill proaktivt begära att en administratör ger behörighet åt hela klient organisationen. Du måste också använda den här slut punkten för att begära program behörigheter (som inte kan begäras med hjälp av auktorisera slut punkten).

Om du följer de här stegen kan din app begära behörigheter för alla användare i en klient, inklusive administrations begränsade omfång. Detta är en åtgärd med hög behörighet och bör bara utföras om det behövs för ditt scenario.

Om du vill se ett kod exempel som implementerar stegen går du till [exemplet admin-begränsade omfattningar](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2).

### <a name="request-the-permissions-in-the-app-registration-portal"></a>Begär behörigheterna i appens registrerings Portal

Administratörs medgivande accepterar inte en omfattnings parameter, så alla behörigheter som begärs måste definieras statiskt i programmets registrering. I allmänhet är det bäst att se till att de behörigheter som definierats statiskt för ett visst program är en supermängd av de behörigheter som begärs dynamiskt/stegvis.

#### <a name="to-configure-the-list-of-statically-requested-permissions-for-an-application"></a>Konfigurera listan med statiskt begärda behörigheter för ett program

1. Gå till ditt program i [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) erfarenhet eller [skapa en app](quickstart-register-app.md) om du inte redan har gjort det.
2. Leta upp avsnittet **API-behörigheter** och i API-behörigheterna klickar du på Lägg till en behörighet.
3. Välj **Microsoft Graph** i listan över tillgängliga API: er och Lägg sedan till de behörigheter som din app kräver.
3. **Spara** appens registrering.

### <a name="recommended-sign-the-user-into-your-app"></a>Rekommenderas Signera användaren till din app

När du skapar ett program som använder den administrativa medgivande slut punkten behöver appen normalt en sida eller vy där administratören kan godkänna appens behörigheter. Den här sidan kan ingå i appens registrerings flöde, en del av appens inställningar, eller så kan det vara ett dedikerat "Connect"-flöde. I många fall är det meningsfullt att appen visar vyn "Anslut" bara när en användare har loggat in med ett arbets-eller skol Microsoft-konto.

När du registrerar användaren i din app kan du identifiera den organisation som administratören tillhör innan de ber dem att godkänna de nödvändiga behörigheterna. Även om det inte är absolut nödvändigt kan det hjälpa dig att skapa en mer intuitiv upplevelse för dina organisations användare. Följ våra självstudier för [Microsoft Identity Platform Protocol](active-directory-v2-protocols.md)för att logga in användaren i.

### <a name="request-the-permissions-from-a-directory-admin"></a>Begär behörigheter från en katalog administratör

När du är redo att begära behörigheter från din organisations administratör kan du omdirigera användaren till Microsoft Identity Platform *admin medgivande*-slutpunkten.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the below request in a browser!
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parameter | Tillstånd | Beskrivning |
| --- | --- | --- |
| `tenant` | Obligatorisk | Den katalog klient som du vill begära behörighet från. Kan anges i GUID eller eget namn format eller allmänt refereras till `common` som visas i exemplet. |
| `client_id` | Obligatorisk | **Program-ID: t (klienten)** som [Azure Portal – Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) -upplevelsen som har tilldelats din app. |
| `redirect_uri` | Obligatorisk |Den omdirigerings-URI där du vill att svaret på din app ska hanteras. Det måste exakt matcha en av de omdirigerings-URI: er som du registrerade i registrerings portalen för appen. |
| `state` | Rekommenderas | Ett värde som ingår i begäran som också kommer att returneras i svaret från token. Det kan vara en sträng med valfritt innehåll som du vill ha. Använd tillstånd för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |

I det här läget kräver Azure AD en klient administratör för att logga in för att slutföra begäran. Administratören uppmanas att godkänna alla behörigheter som du har begärt för din app i registrerings portalen för appen.

#### <a name="successful-response"></a>Lyckat svar

Om administratören godkänner behörigheterna för din app ser det lyckade svaret ut så här:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | Beskrivning |
| --- | --- |
| `tenant` | Den katalog klient som beviljade programmet de behörigheter som begärdes, i GUID-format. |
| `state` | Ett värde som ingår i begäran som också kommer att returneras i svaret från token. Det kan vara en sträng med valfritt innehåll som du vill ha. Statusen används för att koda information om användarens tillstånd i appen innan autentiseringsbegäran inträffade, t. ex. sidan eller vyn de var på. |
| `admin_consent` | Kommer att anges till `True`. |

#### <a name="error-response"></a>Fel svar

Om administratören inte godkänner behörigheterna för din app ser det misslyckade svaret ut så här:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | Beskrivning |
| --- | --- |
| `error` | En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som kan användas för att reagera på fel. |
| `error_description` | Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken till ett fel. |

När du har fått ett lyckat svar från slut punkten för administratörs medgivande har appen fått de behörigheter som begärdes. Sedan kan du begära en token för den resurs som du vill använda.

## <a name="using-permissions"></a>Använda behörigheter

När användaren har fått behörighet till appen kan appen Hämta åtkomsttoken som representerar appens behörighet att få åtkomst till en resurs i viss kapacitet. En åtkomsttoken kan bara användas för en enda resurs, men kodad i åtkomsttoken är alla behörigheter som din app har beviljats för resursen. För att få en åtkomsttoken kan din app göra en begäran till Microsoft Identity Platform token-slutpunkten, så här:

```
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

Du kan använda den resulterande åtkomsttoken i HTTP-begäranden till resursen. Det tyder tillförlitligt på den resurs som din app har rätt behörighet för att utföra en speciell uppgift. 

Mer information om OAuth 2,0-protokollet och hur du hämtar åtkomsttoken finns i [protokoll referens för Microsoft Identity Platform](active-directory-v2-protocols.md).

## <a name="the-default-scope"></a>/.Default-omfånget

Du kan använda `/.default` omfånget för att migrera dina appar från v 1.0-slut punkten till Microsoft Identity Platform-slutpunkten. Det här är en inbyggd omfattning för varje program som refererar till den statiska listan med behörigheter som kon figurer ATS i program registreringen. Värdet är samma sak som v 1.0 `resource=https://graph.microsoft.com` -slut punkterna – dvs begär en token med scope på Microsoft Graph att programmet har registrerats för i Azure Portal. `https://graph.microsoft.com/.default` `scope`

/.Default-omfånget kan användas i alla OAuth 2,0-flöden, men det är nödvändigt i flödet för det aktiva [flödet](v2-oauth2-on-behalf-of-flow.md) och [klientens autentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md).  

> [!NOTE]
> Klienter kan inte kombinera statiska`/.default`() och dynamiskt medgivande i en enskild begäran. `scope=https://graph.microsoft.com/.default+mail.read` Därför leder det till ett fel på grund av en kombination av omfångs typer.

### <a name="default-and-consent"></a>/.default och medgivande

Omfattningen utlöser även slut punkts beteendet `prompt=consent` för v 1.0. `/.default` Den begär tillstånd för alla behörigheter som registrerats av programmet, oavsett resurs. Om `/.default` omfånget ingår som en del av begäran, returnerar omfånget en token som innehåller omfattningarna för den begärda resursen.

### <a name="default-when-the-user-has-already-given-consent"></a>/.default när användaren redan har gett sitt medgivande

Eftersom `/.default` är funktions identiskt för beteendet för den `resource`-koncentriska v 1.0-slutpunkten, tar den med sig även av medgivande beteendet för v 1.0-slut punkten. Dvs utlöser bara en medgivande- `/.default` prompt om ingen behörighet har beviljats mellan klienten och resursen av användaren. Om ett sådant medgivande finns returneras en token som innehåller alla omfattningar som beviljats av användaren för resursen. Men om ingen behörighet har beviljats eller `prompt=consent` parametern har angetts visas en medgivande-prompt för alla omfattningar som registrerats av klient programmet.

#### <a name="example-1-the-user-or-tenant-admin-has-granted-permissions"></a>Exempel 1: Användaren eller klient organisationens administratör har beviljat behörigheter

Användaren (eller klient administratören) har beviljat klienten Microsoft Graph behörigheter `mail.read` och. `user.read` Om klienten gör en begäran om `scope=https://graph.microsoft.com/.default`så visas ingen medgivande fråga oavsett innehållet i de klient program som har registrerat behörigheter för Microsoft Graph. En token returneras som innehåller omfattningarna `mail.read` och. `user.read`

#### <a name="example-2-the-user-hasnt-granted-permissions-between-the-client-and-the-resource"></a>Exempel 2: Användaren har inte beviljats behörigheter mellan klienten och resursen

Det finns inget medgivande för användaren mellan klienten och Microsoft Graph. Klienten har registrerat sig för `user.read` -och `contacts.read` -behörigheterna, samt Azure Key Vault omfattning. `https://vault.azure.net/user_impersonation` När klienten begär en token för `scope=https://graph.microsoft.com/.default`, kommer användaren att se ett medgivande fönster `user.read`för-, `contacts.read`-och Key Vault `user_impersonation` -omfattningarna. Den returnerade token har bara `user.read` omfattningarna och `contacts.read` .

#### <a name="example-3-the-user-has-consented-and-the-client-requests-additional-scopes"></a>Exempel 3: Användaren har samtyckt och klienten begär ytterligare omfång

Användaren har redan samtyckt till `mail.read` för klienten. Klienten har registrerats för `contacts.read` omfånget i registreringen. När klienten gör en begäran om en token med `scope=https://graph.microsoft.com/.default` hjälp av och begär `prompt=consent`medgivande via, kommer användaren bara att se en medgivande skärm för och alla behörigheter som registrerats av programmet. `contacts.read`kommer att finnas på medgivande skärmen, men `mail.read` kommer inte. Den returnerade token är för Microsoft Graph och kommer `mail.read` att `contacts.read`innehålla och.

### <a name="using-the-default-scope-with-the-client"></a>Använda/.default-scope med klienten

Det finns ett specialfall av `/.default` omfånget där en klient begär sitt `/.default` eget omfång. Följande exempel visar det här scenariot.

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
response_type=token            //code or a hybrid flow is also possible here
&client_id=9ada6f8a-6d83-41bc-b169-a306c21527a5
&scope=9ada6f8a-6d83-41bc-b169-a306c21527a5/.default
&redirect_uri=https%3A%2F%2Flocalhost
&state=1234
```

Detta skapar en godkännande skärm för alla registrerade behörigheter (om det är tillämpligt baserat på ovanstående beskrivningar av medgivande `/.default`och), returnerar en id_token i stället för en åtkomsttoken.  Det här beteendet finns för vissa äldre klienter som flyttas från ADAL till MSAL och ska inte användas av nya klienter som är riktade till Microsoft Identity Platform-slutpunkten.  

## <a name="troubleshooting-permissions-and-consent"></a>Felsöka behörigheter och medgivande

Om du eller ditt programs användare ser oväntade fel under medgivande processen, se den här artikeln för fel söknings steg: Ett [oväntat fel inträffade vid godkännande av ett program](../manage-apps/application-sign-in-unexpected-user-consent-error.md).
