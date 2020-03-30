---
title: ADAL till MSAL migreringsguide för Android | Azure
description: Lär dig hur du migrerar din ADAL-app (Azure Active Directory Authentication Library) till MICROSOFT Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: 21866bb7dab3d5a093ffc4655161b80853eadfc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084050"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>ADAL till MSAL-migreringsguide för Android

I den här artikeln beskrivs de ändringar du behöver göra för att migrera en app som använder Azure Active Directory Authentication Library (ADAL) för att använda Microsoft Authentication Library (MSAL).

## <a name="difference-highlights"></a>Skillnaden höjdpunkter

ADAL fungerar med Azure Active Directory v1.0-slutpunkten. Microsoft Authentication Library (MSAL) fungerar med Microsoft identity-plattformen – tidigare känt som Slutpunkten för Azure Active Directory v2.0. Microsoft-identitetsplattformen skiljer sig från Azure Active Directory v1.0 genom att den:

Stöder:
  - Organisationsidentitet (Azure Active Directory)
  - Icke-organisatoriska identiteter som Outlook.com, Xbox Live och så vidare
  - (Endast B2C) Federerad inloggning med Google, Facebook, Twitter och Amazon

- Är standarder kompatibla med:
  - OAuth v2.0
  - OpenID Connect (OIDC)

Det offentliga PROGRAMMET FÖR MSAL introducerar viktiga ändringar, bland annat:

- En ny modell för åtkomst till token:
  - ADAL ger åtkomst till `AuthenticationContext`token via , som representerar servern. MSAL ger åtkomst till `PublicClientApplication`token via , som representerar klienten. Klientutvecklare behöver inte skapa en `PublicClientApplication` ny instans för varje myndighet de behöver interagera med. Endast `PublicClientApplication` en konfiguration krävs.
  - Stöd för att begära åtkomsttoken med hjälp av scope utöver resursidentifierare.
  - Stöd för inkrementellt samtycke. Utvecklare kan begära scope när användaren får åtkomst till fler och fler funktioner i appen, inklusive de som inte ingår under appregistreringen.
  - Myndigheterna valideras inte längre vid körning. I stället deklarerar utvecklaren en lista över "kända myndigheter" under utvecklingen.
- Ändringar av token-API:
  - I ADAL `AcquireToken()` gör först en tyst begäran. Annars gör det en interaktiv begäran. Detta resulterade i att vissa `AcquireToken`utvecklare endast förlitade sig på , vilket resulterade i att användaren oväntat föranleddes för autentiseringsuppgifter ibland. MSAL kräver att utvecklare är avsiktliga om när användaren får en användargränssnittsfråga.
    - `AcquireTokenSilent`alltid resulterar i en tyst begäran som antingen lyckas eller misslyckas.
    - `AcquireToken`alltid resulterar i en begäran som frågar användaren via användargränssnittet.
- MSAL stöder inloggning från antingen en standardwebbläsare eller en inbäddad webbvy:
  - Som standard används standardwebbläsaren på enheten. Detta gör det möjligt för MSAL att använda autentiseringstillstånd (cookies) som redan finns för ett eller flera inloggade konton. Om det inte finns något autentiseringstillstånd, resulterar autentisering under auktorisering via MSAL att autentiseringstillstånd (cookies) skapas till förmån för andra webbprogram som ska användas i samma webbläsare.
- Ny undantagsmodell:
  - Undantag definierar tydligare vilken typ av fel som uppstod och vad utvecklaren behöver göra för att lösa det.
- MSAL stöder parameterobjekt för `AcquireToken` och `AcquireTokenSilent` anrop.
- MSAL stöder deklarativ konfiguration för:
  - Klient-ID, Omdirigera URI.
  - Inbäddad vs standardwebbläsare
  - Myndigheterna
  - HTTP-inställningar som tidsgränsen för läsning och anslutning

## <a name="your-app-registration-and-migration-to-msal"></a>Din appregistrering och migrering till MSAL

Du behöver inte ändra din befintliga appregistrering för att använda MSAL. Om du vill dra nytta av inkrementellt/progressivt medgivande kan du behöva granska registreringen för att identifiera de specifika scope som du vill begära stegvis. Mer information om omfattningar och inkrementellt samtycke följer.

I din appregistrering i portalen visas fliken **API-behörigheter.** Detta innehåller en lista över API:er och behörigheter (scope) som appen för närvarande är konfigurerad för att begära åtkomst till. Den visar också en lista över scopenamn som är associerade med varje API-behörighet.

### <a name="user-consent"></a>Användargodkännande

Med ADAL och AAD v1-slutpunkten beviljades användarens medgivande till resurser som de äger vid första användningen. Med MSAL och Microsofts identitetsplattform kan samtycke begäras stegvis. Inkrementellt medgivande är användbart för behörigheter som en användare kan överväga hög behörighet, eller på annat sätt kan ifrågasätta om de inte får en tydlig förklaring till varför behörigheten krävs. I ADAL kan dessa behörigheter ha resulterat i att användaren överger inloggningen i din app.

> [!TIP]
> Vi rekommenderar att du använder inkrementellt medgivande i scenarier där du behöver ge ytterligare kontext till din användare om varför din app behöver en behörighet.

### <a name="admin-consent"></a>Administratörsmedgivande

Organisationsadministratörer kan samtycka till behörigheter som ditt program kräver för alla medlemmar i organisationen. Vissa organisationer tillåter bara administratörer att godkänna program. Administratörsmedgivande kräver att du inkluderar alla API-behörigheter och omfattningar som används av ditt program i din appregistrering.

> [!TIP]
> Även om du kan begära ett scope med MSAL för något som inte ingår i din appregistrering rekommenderar vi att du uppdaterar appregistreringen så att den innehåller alla resurser och scope som en användare någonsin kan bevilja behörighet till.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migrera från resurs-ID:er till scope

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Autentisera och begära auktorisering för alla behörigheter vid första användningen

Om du för närvarande använder ADAL och inte behöver använda inkrementellt medgivande är det `acquireToken` enklaste `AcquireTokenParameter` sättet att börja använda MSAL att göra en begäran med det nya objektet och ange resurs-ID-värdet.

> [!CAUTION]
> Det går inte att ange både scope och ett resurs-ID. Om du försöker ställa `IllegalArgumentException`in båda kommer det att resultera i en .

 Detta resulterar i samma v1-beteende som du används. Alla behörigheter som begärs i appregistreringen begärs från användaren under den första interaktionen.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Autentisera och begär endast behörigheter efter behov

Om du vill dra nytta av inkrementellt medgivande gör du en lista över behörigheter (scope) som appen använder från din appregistrering och ordnar dem i två listor baserat på:

- Vilka scope du vill begära under användarens första interaktion med appen under inloggningen.
- De behörigheter som är associerade med en viktig funktion i din app som du också måste förklara för användaren.

När du har ordnat scope, organisera varje lista efter vilken resurs (API) du vill begära en token för. Förutom alla andra scope som du vill att användaren ska auktorisera samtidigt.

Det parameterobjekt som används för att göra din begäran till MSAL stöder:

- `Scope`: Listan över scope som du vill begära auktorisering för och ta emot en åtkomsttoken.
- `ExtraScopesToConsent`: Ytterligare en lista över scope som du vill begära auktorisering för när du begär en åtkomsttoken för en annan resurs. Med den här listan över scope kan du minimera antalet gånger som du behöver begära användarauktorisering. Vilket innebär färre uppmaningar om användarauktorisering eller medgivande.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migrera från AuthenticationContext till PublicClientApplications

### <a name="constructing-publicclientapplication"></a>Konstruera PublicClientApplication

När du använder MSAL instansierar du en `PublicClientApplication`. Detta objekt modellerar din appidentitet och används för att göra förfrågningar till en eller flera myndigheter. Med det här objektet ska du konfigurera klientidentiteten, omdirigera URI, standardutfärdare, om du vill använda enhetsbläddraren kontra inbäddad webbvy, loggnivån med mera.

You can declaratively configure this object with JSON, which you either provide as a file or store as a resource within your APK.

Även om det här objektet inte är `Executors` en singleton, internt använder det delas för både interaktiva och tysta begäranden.

### <a name="business-to-business"></a>Business to Business

I ADAL kräver varje organisation som du begär åtkomsttoken från en separat instans av `AuthenticationContext`. I MSAL är detta inte längre ett krav. Du kan ange från vilken behörighet du vill begära en token som en del av din tysta eller interaktiva begäran.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migrera från myndighetsvalidering till kända myndigheter

MSAL har ingen flagga för att aktivera eller inaktivera validering av en myndighet. Myndighetsvalidering är en funktion i ADAL och i de tidiga versionerna av MSAL, som förhindrar att koden begär token från en potentiellt skadlig myndighet. MSAL hämtar nu en lista över myndigheter som Microsoft känner till och slår samman listan med de myndigheter som du har angett i konfigurationen.

> [!TIP]
> Om du är en Azure Business to Consumer (B2C) användare, innebär detta att du inte längre behöver inaktivera validering av auktoritet. Inkludera i stället var och en av azure AD B2C-principer som stöds som myndigheter i din MSAL-konfiguration.

Om du försöker använda en myndighet som inte är känd för Microsoft och inte `UnknownAuthorityException`ingår i konfigurationen får du en .

### <a name="logging"></a>Loggning
Du kan nu deklarativt konfigurera loggning som en del av konfigurationen, så här:

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>Migrera från UserInfo till konto

I ADAL `AuthenticationResult` tillhandahåller `UserInfo` det ett objekt som används för att hämta information om det autentiserade kontot. Termen "användare", vilket innebar en människa eller programvaruagent, tillämpades på ett sätt som gjorde det svårt att kommunicera att vissa appar stöder en enda användare (oavsett om det är en människa eller programvaruagent) som har flera konton.

Tänk på ett bankkonto. Du kan ha mer än ett konto hos mer än ett finansinstitut. När du öppnar ett konto får du (användaren) autentiseringsuppgifter, till exempel ett ATM-kort & PIN-kod, som används för att komma åt ditt saldo, fakturabetalningar och så vidare för varje konto. Dessa autentiseringsuppgifter kan endast användas hos det finansinstitut som utfärdade dem.

På motsvarande sätt, som konton på ett finansinstitut, används konton i Microsofts identitetsplattform med hjälp av autentiseringsuppgifter. Dessa autentiseringsuppgifter är antingen registrerade hos eller utfärdade av Microsoft. Eller av Microsoft på uppdrag av en organisation.

Om Microsofts identitetsplattform skiljer sig från ett finansinstitut, i den här analogin, är att Microsofts identitetsplattform tillhandahåller ett ramverk som gör det möjligt för en användare att använda ett konto och dess associerade autentiseringsuppgifter för att komma åt resurser som tillhör flera individer och organisationer. Det är som att kunna använda ett kort som utfärdats av en bank, på ännu ett finansinstitut. Detta fungerar eftersom alla organisationer i fråga använder Microsofts identitetsplattform, vilket gör att ett konto kan användas i flera organisationer. Här är ett exempel:

Sam arbetar för Contoso.com men hanterar virtuella Azure-datorer som tillhör Fabrikam.com. För att Sam ska kunna hantera Fabrikams virtuella datorer måste han ha behörighet att komma åt dem. Den här åtkomsten kan beviljas genom att lägga till Sams konto i Fabrikam.com och ge hans konto en roll som gör att han kan arbeta med de virtuella datorerna. Detta skulle göras med Azure-portalen.

Om du lägger till Sams Contoso.com-konto som medlem i Fabrikam.com skapas en ny post i Fabrikam.com:s Azure Active Directory for Sam. Sams post i Azure Active Directory kallas ett användarobjekt. I det här fallet pekar det användarobjektet tillbaka till Sams användarobjekt i Contoso.com. Sams Fabrikam-användarobjekt är den lokala representationen av Sam och används för att lagra information om kontot som är associerat med Sam i samband med Fabrikam.com. I Contoso.com är Sams titel Senior DevOps Consultant. I Fabrikam är Sams titel Entreprenör-Virtuella datorer. I Contoso.com är Sam inte ansvarig, eller auktoriserad, att hantera virtuella datorer. I Fabrikam.com är det hans enda jobbfunktion. Men Sam har fortfarande bara en uppsättning referenser att hålla reda på, vilket är de referenser som utfärdats av Contoso.com.

När ett `acquireToken` lyckat samtal har gjorts visas `IAccount` en referens till `acquireTokenSilent` ett objekt som kan användas i senare begäranden.

### <a name="imultitenantaccount"></a>IMultiTenantKonto

Om du har en app som använder anspråk om ett konto från var och `IAccount` en `IMultiTenantAccount`av de klienter där kontot är representerat kan du casta objekt till . Det här gränssnittet `ITenantProfiles`innehåller en karta över , som är nyckeltokats av klient-ID, som gör att du kan komma åt anspråk som tillhör kontot i var och en av klienterna som du har begärt en token från, i förhållande till det aktuella kontot.

Anspråken i roten `IAccount` `IMultiTenantAccount` av och innehåller alltid anspråk från hem hyresgästen. Om du ännu inte har gjort en begäran om en token i hemklienten kommer den här samlingen att vara tom.

## <a name="other-changes"></a>Andra ändringar

### <a name="use-the-new-authenticationcallback"></a>Använda den nya AuthenticationCallback

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}


```

## <a name="migrate-to-the-new-exceptions"></a>Migrera till de nya undantagen

I ADAL finns det en typ `AuthenticationException`av undantag, som innehåller en `ADALError` metod för att hämta uppräkningsvärdet.
I MSAL finns det en hierarki med undantag och var och en har sin egen uppsättning associerade specifika felkoder.

Lista över MSAL-undantag

|Undantag  | Beskrivning  |
|---------|---------|
| `MsalException`     | Standardkontrollerat undantag som genereras av MSAL.  |
| `MsalClientException`     | Genereras om felet är klientsidan. |
| `MsalArgumentException`     | Kastas om ett eller flera indataargument är ogiltiga. |
| `MsalClientException`     | Genereras om felet är klientsidan. |
| `MsalServiceException`     | Genereras om felet är serversidan. |
| `MsalUserCancelException`     | Kastas om användaren avbröt autentiseringsflödet.  |
| `MsalUiRequiredException`     | Kastas om token inte kan uppdateras tyst.  |
| `MsalDeclinedScopeException`     | Kastas om en eller flera begärda scope har avvisats av servern.  |
| `MsalIntuneAppProtectionPolicyRequiredException` | Genereras om resursen har MAMCA-skyddsprincipen aktiverad. |

### <a name="adalerror-to-msalexception-errorcode"></a>ADALError till MsalException ErrorCode

### <a name="adal-logging-to-msal-logging"></a>ADAL-loggning till MSAL-loggning

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
            @Override
            public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
                logs.append(message).append('\n');
            }
        });

// New Log Levels:
public enum LogLevel
{
        /**
         * Error level logging.
         */
        ERROR,
        /**
         * Warning level logging.
         */
        WARNING,
        /**
         * Info level logging.
         */
        INFO,
        /**
         * Verbose level logging.
         */
        VERBOSE
}
```
