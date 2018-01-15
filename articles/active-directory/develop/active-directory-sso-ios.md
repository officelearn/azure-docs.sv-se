---
title: "Aktivera enkel inloggning mellan appar på iOS använder ADAL | Microsoft Docs"
description: "Hur du använder funktionerna i ADAL SDK för att aktivera enkel inloggning i ditt program. "
services: active-directory
documentationcenter: 
author: brandwe
manager: mtillman
editor: 
ms.assetid: d042d6da-7503-4e20-bb55-06917de01fcd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 04/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: a7d93fe6289ade7fbdf3050d49184feb8b370bb5
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2018
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Aktivera enkel inloggning mellan appar på iOS använder ADAL
Tillhandahåller enkel inloggning (SSO) så att användarna behöver bara ange sina autentiseringsuppgifter en gång och dessa autentiseringsuppgifter automatiskt fungerar över förväntade program nu av kunder. Svårt att ange sina användarnamn och lösenord på en liten skärm, ofta gånger kombineras med ytterligare en faktor (2FA) som ett telefonsamtal eller en textläge kod leder till att snabbt klagomål om en användare har att göra det mer än en gång till produkten.

Dessutom, om du använder en identity-plattform som andra program kan använda, till exempel Microsoft Accounts eller ett arbetskonto från Office365 kunder förväntar sig att de autentiseringsuppgifter som ska vara tillgängliga för användning i alla program oavsett leverantören.

Microsoft Identity-plattformen, tillsammans med vår Microsoft Identity-SDK: er, gör den här tunga arbetet åt dig och ger dig möjlighet att glädje dina kunder med enkel inloggning för antingen inom en egen uppsättning program eller, som i våra broker kapaciteten och autentiseraren program över hela enheten.

Den här genomgången kommer information om hur du konfigurerar våra SDK i ditt program att tillhandahålla förmånen till dina kunder.

Den här genomgången gäller för:

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B
* Villkorsstyrd åtkomst med Azure Active Directory

Föregående dokumentet förutsätter att du vet hur du [etablera program i den äldra portalen för Azure Active Directory](active-directory-how-to-integrate.md) och integrerade programmet med den [Microsoft Identity iOS SDK](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>SSO begrepp i Microsoft Identity-plattformen
### <a name="microsoft-identity-brokers"></a>Microsoft Identity mäklare
Microsoft tillhandahåller program för varje mobil plattform som gör att för bryggning av autentiseringsuppgifter i program från olika leverantörer och gör för särskilda funktioner som kräver en enda säker plats varifrån att verifiera autentiseringsuppgifterna. Vi kallar dem **mäklare**. På iOS och Android sker dessa mäklare via nedladdningsbara program att kunder installeras fristående eller pushas till enheten av ett företag som hanterar vissa eller alla av enheten för sina anställda. Dessa mäklare stöder Hantera säkerhet för vissa program eller hela enheten baserat på vad IT-administratörer som önskar. Den här funktionen tillhandahålls av en inbyggd i operativsystemet, kända tekniskt Webbautentiseringskoordinatorn väljare av användarkonto i Windows.

Mer information om hur använder vi dessa mäklare och hur dina kunder kan se dem i sina inloggningen flödet för Microsoft Identity-plattformen finns.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Mönster för att logga in på mobila enheter
Åtkomst till autentiseringsuppgifter på enheter följer två grundläggande mönster för Microsoft Identity-plattform:

* Icke-förhandlad assisterad inloggningar
* Broker assisterad inloggningar

#### <a name="non-broker-assisted-logins"></a>Icke-förhandlad assisterad inloggningar
Icke-förhandlad assisterad inloggningar är inloggning upplevelser som inträffa infogade med programmet och använder lokal lagring på enheten för programmet. Lagringen kan delas mellan program men autentiseringsuppgifterna är tätt kopplade till appen eller paket med hjälp av denna autentiseringsuppgift. Du har förmodligen fått det i många mobila program när du anger ett användarnamn och lösenord själva programmet.

Dessa inloggningar har följande fördelar:

* Användarupplevelsen finns helt i programmet.
* Autentiseringsuppgifter kan delas mellan program som har signerats med samma certifikat, tillhandahåller en enkel inloggning till en uppsättning program.
* Kontrollen runt upplevelse av loggning i har angetts för programmet före och efter inloggning.

Dessa inloggningar har följande nackdelar:

* Enkel inloggning på kan inte användarupplevelse över alla appar som använder Microsoft-Identity enbart över de Microsoft-Identities som programmet har konfigurerats.
* Programmet kan inte användas med mer avancerade funktioner för företag, till exempel villkorlig åtkomst eller Använd InTune-produkter.
* Programmet stöder inte certifikatbaserad autentisering för företagsanvändare.

Här är en representation av hur Microsoft Identity SDK fungerar med delad lagring av ditt program för att aktivera enkel inloggning:

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

#### <a name="broker-assisted-logins"></a>Broker assisterad inloggningar
Service Broker-stödd inloggningar är inloggning upplevelser som inträffar i Service broker-programmet och använder lagring och säkerhet för Service broker för att dela autentiseringsuppgifter för alla program på enheten som gäller Microsoft Identity-plattformen. Detta innebär att dina program som förlitar sig på Service broker för inloggning av användare. På iOS och Android sker dessa mäklare via nedladdningsbara program att kunder installeras fristående eller pushas till enheten av ett företag som hanterar enheten för sina användare. Ett exempel på den här typen av program är Microsoft Authenticator-appen på iOS. Den här funktionen tillhandahålls av en inbyggd i operativsystemet, kända tekniskt Webbautentiseringskoordinatorn väljare av användarkonto i Windows.
Upplevelsen varierar efter plattform och ibland kan vara störande för användarna om de inte hanteras på rätt sätt. Du är mest förmodligen är bekant med det här mönstret om du har installerat Facebook-program och använder Facebook ansluta från ett annat program. Microsoft Identity-plattformen använder samma mönster.

För iOS som leder till att en ”övergång” kommer animering där programmet skickas till bakgrunden medan Microsoft Authenticator-program i förgrunden för användaren att välja vilket konto som de vill logga in med.  

För Android och Windows visas väljare av användarkonto ovanpå ditt program som är mindre störande för användaren.

#### <a name="how-the-broker-gets-invoked"></a>Hur Service broker hämtar anropas
Om en kompatibel broker är installerad på enheten som programmet Microsoft Authenticator SDK: er för Microsoft Identity automatiskt att göra arbetet med att aktivera Service broker för dig när en användare anger de vill logga in med ett konto från Microsoft Identity-plattformen. Det här kontot kan vara ett personligt Microsoft-Account, ett arbets eller skolkonto, eller ett konto som du anger och värden i Azure med hjälp av vår B2C och B2B-produkter. 

 #### <a name="how-we-ensure-the-application-is-valid"></a>Hur vi Kontrollera att programmet är giltigt
 
 Behovet av att kontrollera identiteten för ett program anrop Service broker är avgörande för säkerheten som vi tillhandahåller i broker stödd inloggningar. Tillämpar unika identifierare är endast giltiga för ett visst program så att skadliga program kan ”förfalska” legitima program-ID och ta emot token som är avsedd för programmets legitima varken iOS eller Android. För att säkerställa att vi alltid kommunicerar med rätt programmet vid körning, ber vi utvecklare tillhandahålla anpassade redirectURI när de registrerar sina program med Microsoft. **Hur utvecklare ska använda för att skapa den här omdirigerings-URI diskuteras i detalj nedan.** Den här anpassade redirectURI innehåller paket-ID för programmet och säkerställs för att vara unikt för programmet med Apple App Store. När ett program anropar Service broker, frågar Service broker iOS-operativsystem för att tillhandahålla paket-ID som kallas Service broker. Service broker ger detta paket-ID till Microsoft i anropet till vår identitetssystem. Om paket-ID för programmet inte matchar det paket-ID som angetts för oss av utvecklaren under registreringen, kommer vi att neka åtkomst till token för resursen programmet begär. Försäkra dig om att det program som registrerats av utvecklaren tar emot tokens.

**Utvecklaren kan välja mellan om Microsoft Identity SDK anropar Service broker eller använder icke-förhandlad assisterad flödet.** Men om utvecklaren väljer att inte använda flödet broker-stödd de förlora fördelen med att använda enkel inloggning autentiseringsuppgifter att användaren har redan lagt på enheten och förhindrar att deras program som används med funktioner för företag Microsoft ger sina kunder som villkorlig åtkomst, Intune-hanteringsfunktioner och certifikatbaserad autentisering.

Dessa inloggningar har följande fördelar:

* Användaren upplever SSO över sina program oavsett leverantören.
* Programmet kan använda mer avancerade funktioner för företag som villkorlig åtkomst eller Använd InTune-produkter.
* Programmet stöder certifikatbaserad autentisering för användare i verksamheten.
* Mycket mer säker inloggning som identitet för programmet och användaren har verifierats av broker programmet med ytterligare säkerhetsalgoritmer och kryptering.

Dessa inloggningar har följande nackdelar:

* I iOS övergick användaren utanför din programupplevelse medan autentiseringsuppgifter är valt.
* Förlust av möjligheten att hantera inloggningen upplevelsen för kunderna i ditt program.

Här är en representation av hur Microsoft Identity SDK fungerar med broker-program för att aktivera enkel inloggning:

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

Tillsammans med den här bakgrundsinformation som du ska kunna bättre förstå och implementera enkel inloggning i ditt program med hjälp av Microsoft Identity plattform och SDK: er.

## <a name="enabling-cross-app-sso-using-adal"></a>Aktivera enkel inloggning mellan appar med hjälp av ADAL
Här vi använder ADAL iOS SDK till:

* Aktivera icke-förhandlad stödd enkel inloggning för en uppsättning appar
* Aktivera stöd för Service broker-stödd enkel inloggning

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Aktivera enkel inloggning för icke-förhandlad stödd SSO
För icke-förhandlad assisterad SSO över program hantera SDK: er för Microsoft Identity mycket komplex enkel inloggning för dig. Detta inkluderar att hitta rätt användaren i cachen och underhålla en lista över inloggade användare att fråga.

Att aktivera enkel inloggning för program som du äger måste du göra följande:

1. Se till att alla program användare samma klient-ID eller program-ID.
2. Kontrollera att alla dina program delar samma signeringscertifikat från Apple så att du kan dela nyckelringar
3. Begära berättigandet samma nyckelringar för var och en av dina program.
4. Tala om SDK: er för Microsoft Identity delade nyckelringen du vill att vi ska använda.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Med hjälp av samma klient-ID eller program-ID för alla program i din uppsättning appar
För Microsoft Identity-plattformen vet att den har tillåtelse för att dela token i dina program, måste var och en av dina program du dela samma klient-ID eller program-ID. Det här är den unika identifieraren som angavs för dig när du har registrerat din första program i portalen.

Du kanske undrar hur du identifierar olika appar till tjänsten Microsoft Identity om den använder den samma program-ID. Svaret är med i **omdirigerings-URI: er**. Varje program kan ha flera omdirigerings-URI: er registrerade i onboarding-portalen. Varje app i din suite har en annan omdirigerings-URI. Ett exempel på hur detta ser ut understiger:

App1 omdirigerings-URI:`x-msauth-mytestiosapp://com.myapp.mytestapp`

App2 omdirigerings-URI:`x-msauth-mytestiosapp://com.myapp.mytestapp2`

App3 omdirigerings-URI:`x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Dessa kapslas under samma klient-ID / program-ID och slås upp utifrån omdirigerings-URI som du kommer tillbaka till oss i SDK-konfigurationen.

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


*Observera att formatet för dessa omdirigerings-URI: er beskrivs nedan. Du kan använda alla omdirigerings-URI om du vill stödja broker, då de måste se ut ungefär som anges ovan*

#### <a name="create-keychain-sharing-between-applications"></a>Skapa mellan program för delning av nyckelringar
Aktivering av nyckelringsdelning är utanför omfattningen för det här dokumentet och omfattas av Apple i dokumentet [lägga till funktioner](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Vad är viktigt är att du bestämmer vad du vill att nyckelringen anropas och Lägg till den här funktionen i alla program.

När du har rättigheter som är konfigurerat på rätt sätt bör du se en fil i projektkatalogen rätt `entitlements.plist` som innehåller något som liknar följande:

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

När du har berättigandet nyckelringar aktiverat i var och en av dina program och du är redo att använda enkel inloggning kan berätta för Microsoft Identity SDK om nyckelringen med hjälp av följande inställning i din `ADAuthenticationSettings` med följande inställning:

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> När du delar en nyckelring över dina program kan alla program ta bort användare eller värre ta bort alla token för ditt program. Detta är särskilt katastrofal om du har program som förlitar sig på tokens för att background arbete. Dela en nyckelring remove innebär att du måste vara mycket försiktig i alla-åtgärder via SDK: er för Microsoft Identity.
> 
> 

Klart! Microsoft Identity SDK kommer nu att dela autentiseringsuppgifter i alla program. Lista över användare också delas mellan programinstanser.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Aktivera enkel inloggning för broker stödd SSO
Möjligheten för ett program att använda alla broker som är installerad på enheten är **inaktiverad som standard**. För att kunna använda ditt program med Service broker måste du göra ytterligare konfigurering och Lägg till lite kod i ditt program.

Steg att följa är:

1. Aktivera Service broker-läge i din programkod anrop till MS-SDK.
2. Upprätta en ny omdirigerings-URI och ange som både appen och appen registreringen.
3. Registrera en URL-schema.
4. Stöd för iOS9: lägga till en behörighet i filen info.plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Steg 1: Aktivera Service broker-läge i ditt program
Möjligheten för programmet ska använda Service broker är aktiverat när du skapar ”kontexten” eller installationen av autentisering-objektet. Du kan göra detta genom att ange vilken typ av autentiseringsuppgifter i koden:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
Den `AD_CREDENTIALS_AUTO` inställningen tillåter att Microsoft Identity SDK att anropa till Service broker, `AD_CREDENTIALS_EMBEDDED` förhindrar Microsoft Identity SDK anropar till Service broker.

#### <a name="step-2-registering-a-url-scheme"></a>Steg 2: Registrera en URL-schema
Microsoft Identity-plattformen använder URL: er för att anropa Service broker och sedan komma tillbaka till ditt program. Slutför den serveranrop behöver du ett URL-schema som registrerats för ditt program som Microsoft Identity-plattformen vet om. Detta kan vara utöver eventuella andra app-system du kanske redan har registrerat med ditt program.

> [!WARNING]
> Rekommenderar vi att URL-schemat ganska unika för att minimera risken att en annan app som använder samma URL-schemat. Apple tillämpar inte unika URL-scheman som är registrerade i app store.
> 
> 

Nedan visas ett exempel på hur den visas i projektkonfigurationen av. Du kan också göra detta i XCode samt:

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
Vi behöver kontrollera vi kallar tillbaka till ditt program på ett sätt som operativsystemet iOS kan kontrollera för att säkerställa att vi alltid returnera credential-token till rätt program. IOS-operativsystemet som rapporterar till Microsoft-program för broker paket-ID för programmet anropas. Detta kan vara falsk en otillåtna program. Därför kan utnyttja vi detta tillsammans med URI för appen broker för att säkerställa att token som returneras till rätt program. Vi måste du upprätta detta unika omdirigerings-URI för både i ditt program och ange som en omdirigerings-URI i vår developer-portalen.

Omdirigerings-URI måste vara i rätt form av:

`<app-scheme>://<your.bundle.id>`

ex: *x-msauth-mytestiosapp://com.myapp.mytestapp*

Den här omdirigerings-URI måste anges i din app registrering med hjälp av den [Azure-portalen](https://portal.azure.com/). Mer information om registrering av Azure AD app finns [integrera med Azure Active Directory](active-directory-how-to-integrate.md).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Steg 3a: lägga till en omdirigerings-URI i din app och dev portal till stöd för certifikatbaserad autentisering
Till stöd för certifikatbaserad autentisering en andra ”msauth” måste vara registrerad i ditt program och [Azure-portalen](https://portal.azure.com/) att hantera autentisering med datorcertifikat om du vill lägga till som stöder i ditt program.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

ex: *msauth://code/x-msauth-mytestiosapp%3A%2F%2Fcom.myapp.mytestapp*

#### <a name="step-4-ios9-add-a-configuration-parameter-to-your-app"></a>Steg 4: iOS9: Lägg till en konfigurationsparameter i appen
ADAL använder – canOpenURL: Kontrollera om Service broker är installerad på enheten. I iOS låsta 9 Apple scheman ett program kan fråga efter. Du behöver lägga till ”msauth” i avsnittet LSApplicationQueriesSchemes i din `info.plist file`.

<key>LSApplicationQueriesSchemes</key> <array> <string>msauth</string></array>

### <a name="youve-configured-sso"></a>Du har konfigurerat SSO!
Nu Microsoft Identity SDK automatiskt både dela autentiseringsuppgifter i dina program och anropa Service broker om den finns på sin enhet.

