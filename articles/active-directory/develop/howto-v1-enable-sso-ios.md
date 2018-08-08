---
title: Så här aktiverar du enkel inloggning mellan appar på iOS med hjälp av ADAL | Microsoft Docs
description: 'Hur du använder funktionerna i ADAL SDK för att aktivera enkel inloggning i ditt program. '
services: active-directory
author: CelesteDG
manager: mtillman
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/07/2017
ms.author: celested
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 203cb4f57cfa50a17b66a9b70a44568e57ec4835
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/07/2018
ms.locfileid: "39601978"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Så här aktiverar du enkel inloggning mellan appar på iOS med hjälp av ADAL

Tillhandahåller enkel inloggning (SSO) så att användarna behöver bara ange sina autentiseringsuppgifter för en gång och dessa autentiseringsuppgifter automatiskt fungerar på olika förväntas nu program av kunder. Svårigheterna med att ange sitt användarnamn och lösenord på en liten skärm, ofta gånger i kombination med ytterligare en faktor (2FA) som ett telefonsamtal eller en textläge kod, resulterar i snabb klagomål från slutanvändarna om en användare har att göra detta mer än en gång för din produkt.

Dessutom, om du använder en identity-plattformen som andra program kan använda, till exempel Microsoft Accounts eller ett arbetskonto från Office 365, kunderna förväntar sig att de autentiseringsuppgifter som ska vara tillgängliga för användning i alla sina program oavsett leverantören.

Microsoft Identity-plattformen, tillsammans med våra Microsoft Identity-SDK: er, utför den här hårda arbetet åt dig och ger dig möjlighet att fler nöjda kunder med enkel inloggning antingen inom en egen uppsättning program eller, precis som med vår broker funktions- och Authenticator program, över hela enheten.

Den här genomgången kommer berättar hur du konfigurerar vår SDK i ditt program att tillhandahålla den här förmånen till dina kunder.

Den här genomgången gäller för:

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B
* Villkorsstyrd åtkomst med Azure Active Directory

Föregående dokumentet förutsätter att du vet hur du [etablera program i den äldre portalen för Azure Active Directory](active-directory-how-to-integrate.md) och integreras med den [Microsoft Identity iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>SSO-begrepp i Microsoft Identity-plattformen

### <a name="microsoft-identity-brokers"></a>Microsoft Identity Brokers

Microsoft tillhandahåller program för alla mobila plattformar som ska ge överbryggning av autentiseringsuppgifter i program från olika leverantörer och tillåter specialfunktioner som kräver en enda säker plats varifrån att verifiera autentiseringsuppgifterna. Vi kallar dem **mäklare**. På iOS och Android tillhandahålls Broker-programmen via nedladdningsbara program att kunder installeras fristående eller kan skickas till enheten av ett företag som hanterar vissa eller samtliga av enheten för sina anställda. Broker-programmen stöd Hantera säkerhet för vissa program eller hela enheten baserat på vad IT-administratörer vill ha. Den här funktionen tillhandahålls i Windows, en kontoväljare som är inbyggda i operativsystemet, som Webbautentiseringskoordinatorn.

Läs vidare för mer information om hur vi använder dessa asynkrona meddelandeköer och hur dina kunder kan se dem i sina inloggningsflödet för Microsoft Identity-plattformen.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Mönster för att logga in på mobila enheter

Åtkomst till autentiseringsuppgifter på enheter som följer två grundläggande mönster för Microsoft Identity-plattformen:

* Icke-förhandlad assisterad inloggningar
* Mäkla assisterad inloggningar

#### <a name="non-broker-assisted-logins"></a>Icke-förhandlad assisterad inloggningar

Icke-förhandlad assisterad inloggningar är inloggningen upplevelser som inträffar infogade med programmet och använda den lokala lagringen på enheten för programmet. Den här lagringen kan delas mellan program, men autentiseringsuppgifterna är tätt kopplade till appen eller uppsättning appar med hjälp av denna autentiseringsuppgift. Du har förmodligen stött på detta i många mobila program när du anger ett användarnamn och lösenord själva programmet.

Dessa inloggningar har följande fördelar:

* Användarupplevelsen finns helt och hållet i programmet.
* Autentiseringsuppgifter kan delas mellan program som har signerats med samma certifikat, vilket ger en enkel inloggning till din uppsättning program.
* Kontrollen runt upplevelsen av att logga in finns i programmet före och efter inloggning.

Dessa inloggningar har följande nackdelar:

* Enkel inloggning kan inte användarupplevelse i alla appar som använder en Microsoft Identity endast i de Microsoft-Identities som ditt program har konfigurerats.
* Programmet kan inte användas med mer avancerade funktioner, till exempel villkorlig åtkomst eller Använd Intune-uppsättning med produkter.
* Programmet stöder inte certifikatbaserad autentisering för företagsanvändare.

Här är en representation av hur Microsoft Identity-SDK: er fungerar med delad lagring för dina program att aktivera enkel inloggning:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAK SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Mäkla assisterad inloggningar

Broker-assisted inloggningar är inloggningen upplevelser som inträffar inom broker-programmet och Använd storage och säkerheten för den asynkrona meddelandekön för att dela autentiseringsuppgifter för alla program på enheten som gäller Microsoft Identity-plattformen. Det innebär att dina program är beroende av den asynkrona meddelandekön för inloggning av användare. På iOS och Android tillhandahålls Broker-programmen via nedladdningsbara program att kunder installera oberoende av varandra eller till enheten av ett företag som hanterar enheten för sina användare. Ett exempel på den här typen av program är Microsoft Authenticator-program på iOS. Den här funktionen tillhandahålls en kontoväljare som är inbyggda i operativsystemet, som Webbautentiseringskoordinatorn i Windows.
Upplevelsen varierar efter plattform och kan ibland vara störande för användarna om den inte hanteras korrekt. Du är förmodligen mest bekant med det här mönstret om du har installerat Facebook-program och använder Facebook ansluta från ett annat program. Microsoft Identity-plattformen använder samma mönster.

För iOS som detta leder till en ”övergång” kommer animering där ditt program skickas till bakgrunden medan Microsoft Authenticator-program i förgrunden för användaren att välja vilket konto som de vill logga in med. 

För Android och Windows konto Väljaren visas ovanpå ditt program, vilket är mindre störande för användaren.

#### <a name="how-the-broker-gets-invoked"></a>Hur den asynkrona meddelandekön hämtar anropas

Om en kompatibel broker är installerad på enheten, t.ex. Microsoft Authenticator-program, gör Microsoft Identity-SDK: er automatiskt verk som tillhör anropar den asynkrona meddelandekön åt dig när en användare anger de vill logga in med ett konto från Microsoft Identity-plattformen. Det här kontot kan vara ett personligt Microsoft-Account, ett arbets eller skolkonto, eller ett konto som du anger och värd i Azure med hjälp av vår B2C och B2B-produkter.

#### <a name="how-we-ensure-the-application-is-valid"></a>Hur ser vi till att programmet är giltigt
 
 Det är nödvändigt att säkerställa identiteten för ett program-anrop som den asynkrona meddelandekön är avgörande för säkerheten som vi tillhandahåller i broker assisted inloggningar. Framtvingar unika identifierare som är bara giltiga för ett visst program så att skadliga program kan ”förfalska” legitima program-ID och ta emot de token som är avsedd för legitima programmet varken iOS eller Android. För att säkerställa att vi alltid kommunicerar med rätt program under körning, ber vi utvecklare att tillhandahålla en anpassad redirectURI när de registrerar sina program med Microsoft. **Hur utvecklare ska använda för att skapa den här omdirigerings-URI beskrivs i detalj nedan.** Den här anpassade redirectURI innehåller paket-ID för programmet och säkerställs för att vara unikt för programmet med Apple App Store. När ett program anropar den asynkrona meddelandekön, frågar den asynkrona meddelandekön iOS-operativsystem för att ge det paket-ID som kallas den asynkrona meddelandekön. Den asynkrona meddelandekön tillhandahåller detta paket-ID för Microsoft i anropet till vår identitetssystem. Om paket-ID för programmet inte matchar Appsamlings-ID som tillhandahålls till oss av utvecklaren under registreringen, kommer vi att neka åtkomst till token för resursen programmet begär. Den här kontrollen säkerställer att endast program som registrerats av utvecklaren får token.

**Utvecklaren kan välja mellan om Microsoft Identity-SDK anropar den asynkrona meddelandekön eller använder icke-förhandlad assisterad flödet.** Men om utvecklaren väljer att inte använda broker-assisted-flödet de förlora fördelen med att använda SSO autentiseringsuppgifter att användaren har redan lagt på enheten och förhindrar att deras program med Microsoft tillhandahåller funktioner som används för dess kunder som villkorlig åtkomst, hantering av funktioner och certifikatbaserad autentisering.

Dessa inloggningar har följande fördelar:

* SSO användarupplevelser i alla sina program oavsett leverantören.
* Ditt program kan använda mer avancerade funktioner, till exempel villkorlig åtkomst eller Använd Intune-uppsättning med produkter.
* Ditt program har stöd för certifikatbaserad autentisering för företagsanvändare.
* Mycket säkrare inloggning som identitet för programmet och användaren kan verifieras genom broker-program med algoritmer för ytterligare säkerhet och kryptering.

Dessa inloggningar har följande nackdelar:

* I iOS supportprocessen användaren utanför ditt programs upplevelse när autentiseringsuppgifter är valt.
* Förlust av möjligheten att hantera inloggning för dina kunder i ditt program.

Här är en representation av hur Microsoft Identity-SDK: er fungerar med broker-program för att aktivera enkel inloggning:

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

Som enda verktyg med den här bakgrundsinformation du borde att bättre förstå och implementera enkel inloggning i ditt program med hjälp av Microsoft Identity-plattformen och SDK: er.

## <a name="enabling-cross-app-sso-using-adal"></a>Aktivera enkel inloggning mellan appar med hjälp av ADAL

Här använder vi ADAL iOS SDK till:

* Aktivera icke-förhandlad assisterad SSO för din utbud av appar
* Aktivera stöd för broker-assisted SSO

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Aktivera enkel inloggning för icke-förhandlad assisted SSO

För icke-förhandlad assisterad enkel inloggning i program hantera mycket av komplexiteten med enkel inloggning för dig i Microsoft Identity-SDK: er. Detta inkluderar att hitta rätt användaren i cachen och underhålla en lista över inloggade användare som du kan fråga.

Att aktivera enkel inloggning för program som du äger du behöver göra följande:

1. Se till att alla program-användare på samma klient-ID eller program-ID.
2. Kontrollera att alla dina program delar samma signeringscertifikat från Apple så att du kan dela nyckelringar
3. Begär samma nyckelring rättighet för var och en av dina program.
4. Ge Microsoft Identity-SDK: er på delade nyckelringen du vill att vi ska använda.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Med hjälp av samma klient-ID / program-ID: T för alla program i din utbud av appar

För Microsoft Identity-plattformen att se att den tillåter för att dela token i dina program, måste var och en av dina program dela samma klient-ID eller program-ID. Det här är den unika identifieraren som angavs för dig när du registrerade ditt första program i portalen.

Du kanske undrar hur du identifierar olika appar till Microsoft Identity-tjänsten om den använder samma programmets ID. Svaret är med i **omdirigerings-URI: er**. Varje program kan ha flera omdirigerings-URI: er registrerad i onboarding-portalen. Varje app i din suite har en annan omdirigerings-URI. Ett exempel på hur detta ser ut som understiger:

App1 omdirigerings-URI: `x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 omdirigerings-URI: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 omdirigerings-URI: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Dessa kapslas under samma klient-ID / program-ID och söktes baserat på omdirigeringen-URI som du kommer tillbaka till oss i SDK-konfiguration.

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


*Observera att formatet för de här omdirigerings-URI: er beskrivs nedan. Du kan använda valfri omdirigerings-URI om du vill stödja den asynkrona meddelandekön i vilket fall de måste se ut ungefär som ovan*

#### <a name="create-keychain-sharing-between-applications"></a>Skapa nyckelringar mellan program

Aktivering av nyckelringsdelning är utanför omfattningen för det här dokumentet och omfattas av Apple i dokumentet [lägga till funktioner](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Det viktiga är att du bestämmer vad du vill att din nyckelring anropas och Lägg till den här funktionen åt alla dina program.

När du har konfigurerat korrekt rättigheter bör se en fil i projektkatalogen berättigade `entitlements.plist` som innehåller något som liknar följande:

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
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

När du har berättigandet nyckelring aktiverat i var och en av dina program och du är redo att använda enkel inloggning kan berätta för Microsoft Identity-SDK om nyckelringen med hjälp av följande inställning i din `ADAuthenticationSettings` med följande inställning:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> När du delar en nyckelring åt dina program kan alla program ta bort användare eller sämre resultat ta bort alla token för ditt program. Detta är särskilt och med katastrofal om du har program som förlitar sig på tokens för att background arbete. Dela en nyckelring remove innebär att du måste vara mycket försiktig i alla-åtgärder via SDK: er för Microsoft Identity.

Klart! SDK för Microsoft Identity kommer nu att dela autentiseringsuppgifter åt alla dina program. Användarlistan också delas mellan instanser av programmet.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Aktivera enkel inloggning för broker assisted SSO

Möjligheten för ett program att använda alla broker som är installerad på enheten är **inaktiverat som standard**. För att kunna använda ditt program med den asynkrona meddelandekön måste du göra ytterligare konfigurering och lägga till kod i ditt program.

Steg att följa är:

1. Aktivera broker läge i din programkod anrop till MS-SDK.
2. Upprätta en ny omdirigerings-URI och ger som för både appen och appen registreringen.
3. Registrera ett URL-schema.
4. Stöd för iOS9: lägga till en behörighet i info.plist-filen.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Steg 1: Aktivera broker läge i ditt program

Funktioner som gör att programmet ska använda den asynkrona meddelandekön aktiveras när du skapar ”kontexten” eller installationen av de autentisering. Du kan göra detta genom att ange din typ av autentiseringsuppgifter i din kod:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
Den `AD_CREDENTIALS_AUTO` inställningen tillåter att Microsoft Identity-SDK att anropa till den asynkrona meddelandekön `AD_CREDENTIALS_EMBEDDED` förhindrar att Microsoft Identity-SDK anropar till den asynkrona meddelandekön.

#### <a name="step-2-registering-a-url-scheme"></a>Steg 2: Registrera ett URL-schema

Microsoft Identity-plattformen använder URL: er för att anropa den asynkrona meddelandekön och sedan komma tillbaka till ditt program. Du behöver ett URL-schema som har registrerats för ditt program som Microsoft Identity-plattformen vet om för att slutföra den tur och RETUR. Detta kan vara utöver eventuella andra app-system du kanske redan har registrerat med ditt program.

> [!WARNING]
> Rekommenderar vi att URL-schema ganska unika att minimera risken för en annan app som använder samma URL-schema. Apple påtvingar inte unik URL-scheman som är registrerade i app store.
> 
> 

Nedan visas ett exempel på hur det visas i din projektkonfiguration. Du kan också göra detta i XCode samt:

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

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Steg 3: Upprätta en ny omdirigerings-URI med URL-schema

Vi måste se till att vi kallar tillbaka till ditt program på ett sätt som iOS-operativsystemet kan kontrollera för att säkerställa att vi alltid returnerar token som autentiseringsuppgifter till rätt program kan. IOS-operativsystem som rapporterar till Microsoft broker program paket-ID för programmet anropas. Detta kan inte vara falsk genom en otillåtna program. Därför kan utnyttja vi detta tillsammans med URI: N för vårt broker-program så att token som returneras till rätt program. Vi måste du fastställa den här unika omdirigerings-URI både i ditt program och Ställ in som en omdirigerings-URI i vår developer-portalen.

Omdirigerings-URI måste vara i rätt format för:

`<app-scheme>://<your.bundle.id>`

Exempel: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Den här omdirigerings-URI måste anges i din app registrering med den [Azure-portalen](https://portal.azure.com/). Mer information om Azure AD app-registrering finns i [integrera med Azure Active Directory](active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Steg 3a: lägga till en omdirigerings-URI i din app och dev portal stöd för certifikatbaserad autentisering

Till stöd för certifikatbaserad autentisering en andra ”msauth” måste vara registrerad i ditt program och [Azure-portalen](https://portal.azure.com/) att hantera autentisering om du vill lägga till som har stöd för i ditt program.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

Exempel: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Steg 4: iOS9: lägga till en konfigurationsparameter i din app

ADAL använder – canOpenURL: att kontrollera om den asynkrona meddelandekön är installerat på enheten. I iOS låsta 9 Apple scheman ett program kan fråga efter. Du måste lägga till ”msauth” i avsnittet LSApplicationQueriesSchemes i din `info.plist file`.

<key>LSApplicationQueriesSchemes</key> <array> <string>msauth</string></array>

### <a name="youve-configured-sso"></a>Du har konfigurerat SSO!

Nu Microsoft Identity-SDK: N automatiskt både dela autentiseringsuppgifter i dina program och anropar den asynkrona meddelandekön om den finns på enheten.