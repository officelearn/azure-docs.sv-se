---
title: Så här aktiverar du SSO över flera appar på iOS med ADAL | Microsoft-dokument
description: Så här använder du funktionerna i ADAL SDK för att aktivera enkel inloggning i dina program.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 082cbb931c9dae60b39f9ee5323337bf051fb56d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154788"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Så här aktiverar du SSO över flera appar på iOS med ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Med enkel inloggning (SSO) kan användare bara ange sina autentiseringsuppgifter en gång och låta dessa autentiseringsuppgifter automatiskt arbeta mellan program och på olika plattformar som andra program kan använda (till exempel Microsoft-konton eller ett arbetskonto från Microsoft 365) fråga förlaget.

Microsofts identitetsplattform, tillsammans med SDK:erna, gör det enkelt att aktivera SSO i din egen serie av appar, eller med mäklarfunktionen och Authenticator-programmen, över hela enheten.

I det här programmet får du lära dig hur du konfigurerar SDK i programmet så att SSO får till dina kunder.

Denna how-to gäller för:

* Azure Active Directory (Azure Active Directory)
* Azure Active Directory B2C
* Azure Active Directory B2B
* Villkorsåtkomst för Azure Active Directory

## <a name="prerequisites"></a>Krav

Detta instruktioner förutsätter att du vet hur man:

* Etablera din app med hjälp av äldre portal för Azure AD. Mer information finns i [Registrera en app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* Integrera ditt program med [Azure AD iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="single-sign-on-concepts"></a>Enstaka inloggningsbegrepp

### <a name="identity-brokers"></a>Identitetsmäklare

Microsoft tillhandahåller program för varje mobil plattform som möjliggör överbryggande av autentiseringsuppgifter mellan program från olika leverantörer och för förbättrade funktioner som kräver en enda säker plats varifrån autentiseringsuppgifterna kan valideras. Dessa kallas **mäklare**.

På iOS och Android tillhandahålls mäklare via nedladdningsbara program som kunder antingen installerar självständigt eller skjuts till enheten av ett företag som hanterar vissa, eller alla, enheter för sina anställda. Mäklare stöder hantering av säkerhet bara för vissa program eller hela enheten baserat på IT-administratörskonfiguration. I Windows tillhandahålls den här funktionen av en kontoväljare inbyggd i operativsystemet, tekniskt känd som Webbautentiseringsmäklaren.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Mönster för inloggning på mobila enheter

Åtkomst till autentiseringsuppgifter på enheter följer två grundläggande mönster:

* Icke-mäklare assisterade inloggningar
* Mäklare assisterade inloggningar

#### <a name="non-broker-assisted-logins"></a>Icke-mäklare assisterade inloggningar

Icke-mäklare assisterade inloggningar är inloggningsupplevelser som sker i linje med programmet och använda den lokala lagringen på enheten för det programmet. Den här lagringen kan delas mellan program, men autentiseringsuppgifterna är tätt bundna till appen eller paketet med appar som använder den autentiseringsförbehöret. Du har troligen upplevt detta i många mobila applikationer när du anger ett användarnamn och lösenord i själva programmet.

Dessa inloggningar har följande fördelar:

* Användarupplevelsen finns helt i programmet.
* Autentiseringsuppgifter kan delas mellan program som är signerade av samma certifikat, vilket ger en enda inloggningsupplevelse till din programsvit.
* Kontroll över upplevelsen av att logga in ges till programmet före och efter inloggning.

Dessa inloggningar har följande nackdelar:

* Användare kan inte uppleva enkel inloggning i alla appar som använder en Microsoft-identitet, endast i de Microsoft-identiteter som programmet har konfigurerat.
* Programmet kan inte användas med mer avancerade affärsfunktioner som villkorlig åtkomst eller använda Intune-produktpaketet.
* Programmet kan inte stödja certifikatbaserad autentisering för företagsanvändare.

Här är en representation av hur SDK:erna fungerar med delad lagring av dina program för att aktivera SSO:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAL SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Mäklare assisterade inloggningar

Broker-assisted inloggningar är inloggningsupplevelser som uppstår inom mäklaren ansökan och använda lagring och säkerhet för mäklaren att dela autentiseringsuppgifter över alla program på enheten som tillämpar identitetsplattformen. Det innebär att dina program förlitar sig på mäklaren för att logga in användare. På iOS och Android tillhandahålls dessa mäklare via nedladdningsbara program som kunder antingen installerar oberoende av eller skjuts till enheten av ett företag som hanterar enheten för sina användare. Ett exempel på den här typen av program är Microsoft Authenticator-programmet på iOS. I Windows tillhandahålls den här funktionen av en kontoväljare inbyggd i operativsystemet, tekniskt känd som webbautentiseringsmäklaren.

Upplevelsen varierar beroende på plattform och kan ibland vara störande för användarna om den inte hanteras korrekt. Du är förmodligen mest bekant med detta mönster om du har Facebook-programmet installerat och använda Facebook Connect från ett annat program. Identitetsplattformen använder samma mönster.

För iOS leder detta till en "övergångsanimering" där ditt program skickas till bakgrunden medan Microsoft Authenticator-programmen kommer till förgrunden för användaren att välja vilket konto de vill logga in med.

För Android och Windows visas kontoväljaren ovanpå ditt program, vilket är mindre störande för användaren.

#### <a name="how-the-broker-gets-invoked"></a>Hur mäklaren blir åberopad

Om en kompatibel mäklare är installerad på enheten, som Microsoft Authenticator-programmet, kommer SDK:erna automatiskt att göra jobbet för att anropa mäklaren åt dig när en användare anger att de vill logga in med ett konto från identitetsplattformen. Det här kontot kan vara ett personligt Microsoft-konto, ett arbets- eller skolkonto eller ett konto som du tillhandahåller och är värd för i Azure med hjälp av våra B2C- och B2B-produkter.

#### <a name="how-we-ensure-the-application-is-valid"></a>Hur vi säkerställer att ansökan är giltig

Behovet av att säkerställa identiteten på ett program som anropar mäklaren är avgörande för den säkerhet vi tillhandahåller i mäklare assisterade inloggningar. Varken iOS eller Android tillämpar unika identifierare som endast är giltiga för ett visst program, så skadliga program kan "förfalska" ett legitimt programs identifierare och ta emot de token som är avsedda för det legitima programmet. För att säkerställa att vi alltid kommunicerar med rätt program vid körning ber vi utvecklaren att tillhandahålla en anpassad redirectURI när de registrerar sitt program hos Microsoft. Hur utvecklare ska skapa den här omdirigera URI:n diskuteras i detalj nedan. Denna anpassade redirectURI innehåller programmets paket-ID och säkerställs att den är unik för programmet av Apple App Store. När ett program anropar mäklaren ber mäklaren iOS-operativsystemet att förse det med det bundle-ID som ringde mäklaren. Mäklaren tillhandahåller detta paket-ID till Microsoft i samtalet till vårt identitetssystem. Om paketets ID för programmet inte matchar det paket-ID som tillhandahålls oss av utvecklaren under registreringen, kommer vi att neka åtkomst till token för den resurs som programmet begär. Den här kontrollen säkerställer att endast det program som registrerats av utvecklaren tar emot token.

**Utvecklaren har valet om SDK anropar mäklaren eller använder det icke-mäklarens assisterade flöde.** Men om utvecklaren väljer att inte använda det mäklarstödda flödet förlorar de fördelen med att använda SSO-autentiseringsuppgifter som användaren redan har lagt till på enheten och förhindrar att deras program används med affärsfunktioner som Microsoft tillhandahåller kunder som villkorlig åtkomst, Intune-hanteringsfunktioner och certifikatbaserad autentisering.

Dessa inloggningar har följande fördelar:

* Användarupplevelser SSO i alla sina program oavsett leverantör.
* Ditt program kan använda mer avancerade affärsfunktioner som villkorlig åtkomst eller använda Intune-serien med produkter.
* Ditt program kan stödja certifikatbaserad autentisering för företagsanvändare.
* Mycket säkrare inloggningsupplevelse som programmets identitet och användaren verifieras av mäklarprogrammet med ytterligare säkerhetsalgoritmer och kryptering.

Dessa inloggningar har följande nackdelar:

* I iOS övergå användaren från programmets upplevelse medan autentiseringsuppgifter väljs.
* Förlust av möjligheten att hantera inloggningsupplevelsen för dina kunder i din ansökan.

Här är en representation av hur SDK fungerar med mäklaren applikationer för att aktivera SSO:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

## <a name="enabling-cross-app-sso-using-adal"></a>Aktivera SSO över flera appar med ADAL

Här använder vi ADAL iOS SDK för att:

* Aktivera SSO som inte är mäklare för din programsvit
* Aktivera stöd för mäklarassisterad SSO

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Aktivera SSO för icke-mäklare assisterad SSO

För icke-mäklare assisterad SSO över program, SDKs hantera mycket av komplexiteten i SSO för dig. Detta inkluderar att hitta rätt användare i cacheminnet och underhålla en lista över inloggade användare som du kan fråga efter.

För att aktivera SSO över program som du äger måste du göra följande:

1. Se till att alla dina program använder samma klient-ID eller program-ID.
2. Se till att alla dina program delar samma signeringscertifikat från Apple så att du kan dela nyckelringar.
3. Begär samma nyckelringsrätt för var och en av dina ansökningar.
4. Berätta för SDK:erna om den delade nyckelring du vill att vi ska använda.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Använda samma klient-ID /Application ID för alla program i din svit av appar

För att identitetsplattformen ska veta att det är tillåtet att dela token i dina program måste var och en av dina program dela samma klient-ID eller application-ID. Detta är den unika identifierare som angavs till dig när du registrerade ditt första program i portalen.

Med omdirigerings-URI:er kan du identifiera olika appar till Microsofts identitetstjänst om de använder samma program-ID. Varje program kan ha flera omdirigera URI:er registrerade i introduktionsportalen. Varje app i din svit kommer att ha en annan omdirigera URI. Ett exempel på hur det här ser ut finns nedan:

App1 Omdirigera URI:`x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 Omdirigera URI:`x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 Omdirigera URI:`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Dessa är kapslade under samma klient-ID / applikations-ID och tittade upp baserat på den omdirigera URI du returnerar till oss i din SDK-konfiguration.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```

Formatet på dessa omdirigerings-URI:er förklaras nedan. Du kan använda någon Redirect URI om du inte vill stödja mäklaren, i vilket fall de måste se ut ungefär som ovanstående *

#### <a name="create-keychain-sharing-between-applications"></a>Skapa nyckelringsdelning mellan program

Att aktivera nyckelringsdelning ligger utanför det här dokumentets räckvidd och omfattas av Apple i deras dokument [Lägga till funktioner](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Det viktiga är att du bestämmer vad du vill att nyckelringen ska anropas och lägger till den funktionen i alla dina program.

När du har rätt behörigheter bör du se en fil `entitlements.plist` i projektkatalogen som innehåller något som ser ut som följande:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

När du har aktiverat nyckelringsberättigandet i vart och ett av dina program och du är redo att använda SSO kan du berätta för identiteten SDK om nyckelringen genom att använda följande inställning i följande `ADAuthenticationSettings` inställning:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> När du delar en nyckelring i dina program kan alla program ta bort användare eller ta bort alla token i ditt program. Detta är särskilt katastrofalt om du har program som förlitar sig på tokens för att göra bakgrundsarbete. Att dela en nyckelring innebär att du måste vara mycket försiktig i alla åtgärder för att ta bort dem via identitets-SDK:erna.

Klart! SDK kommer nu att dela autentiseringsuppgifter över alla dina program. Användarlistan delas också mellan programinstanser.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Aktivera SSO för mäklare assisterad SSO

Möjligheten för ett program att använda någon mäklare som är installerad på enheten är **inaktiverad som standard**. För att kunna använda ditt program med mäklaren måste du göra ytterligare konfiguration och lägga till lite kod till ditt program.

Stegen att följa är:

1. Aktivera mäklarläge i programkodens anrop till MS SDK.
2. Upprätta en ny omdirigera URI och ange det för både appen och din appregistrering.
3. Registrera ett URL-schema.
4. Lägg till en behörighet i filen info.plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Steg 1: Aktivera mäklarläge i ditt program

Möjligheten för ditt program att använda mäklaren är aktiverad när du skapar "kontexten" eller den första installationen av ditt autentiseringsobjekt. Du gör detta genom att ange dina autentiseringsuppgifter typ i koden:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
Inställningen `AD_CREDENTIALS_AUTO` gör det möjligt för SDK att försöka `AD_CREDENTIALS_EMBEDDED` ringa ut till mäklaren, kommer att hindra SDK från att ringa till mäklaren.

#### <a name="step-2-registering-a-url-scheme"></a>Steg 2: Registrera ett URL-schema

Identitetsplattformen använder webbadresser för att anropa mäklaren och sedan returnera kontrollen tillbaka till ditt program. För att avsluta den rundturen behöver du ett URL-schema registrerat för ditt program som identitetsplattformen kommer att känna till. Detta kan vara ett tillägg till alla andra appscheman som du tidigare har registrerat med ditt program.

> [!WARNING]
> Vi rekommenderar att url-schemat är ganska unikt för att minimera risken för att en annan app använder samma URL-schema. Apple tillämpar inte det unika med url-scheman som är registrerade i App Store.

Nedan följer ett exempel på hur detta visas i projektkonfigurationen. Du kan också göra detta i XCode också:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Steg 3: Upprätta en ny omdirigera URI med ditt URL-schema

För att säkerställa att vi alltid returnerar autentiseringstoken till rätt program måste vi se till att vi ringer tillbaka till din ansökan på ett sätt som iOS-operativsystemet kan verifiera. IOS-operativsystemet rapporterar till Microsoft-mäklarprogrammen bundle-ID för det program som anropar det. Detta kan inte förfalskas av en otillåten ansökan. Därför utnyttjar vi detta tillsammans med URI för vår mäklarapplikation för att säkerställa att tokens returneras till rätt program. Vi kräver att du upprättar denna unika omdirigera URI både i ditt program och som en Redirect URI i vår utvecklarportal.

Din omdirigera URI måste vara i rätt form av:

`<app-scheme>://<your.bundle.id>`

ex: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Den här omdirigerings-URI:n måste anges i din appregistrering med [Azure-portalen](https://portal.azure.com/). Mer information om registrering av Azure AD-appar finns i [Integrera med Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Steg 3a: Lägga till en omdirigerad URI i appen och utvecklingsportalen för att stödja certifikatbaserad autentisering

För att stödja certbaserad autentisering måste en andra "msauth" registreras i ditt program och [Azure-portalen](https://portal.azure.com/) för att hantera certifikatautentisering om du vill lägga till det stödet i ditt program.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>Steg 4: Lägga till en konfigurationsparameter i appen

ADAL använder –canOpenURL: för att kontrollera om mäklaren är installerad på enheten. I iOS 9 på, Apple låst ner vilka system ett program kan fråga efter. Du måste lägga till "msauth" i avsnittet LSApplicationQueriesSchemes i din `info.plist file`.

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Du har konfigurerat SSO!

Nu kommer identiteten SDK automatiskt både dela autentiseringsuppgifter över dina program och anropa mäklaren om det finns på deras enhet.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [SAML-protokoll för enkel inloggning](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
