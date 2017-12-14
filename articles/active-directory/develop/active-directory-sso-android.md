---
title: "Aktivera enkel inloggning mellan appar på Android använder ADAL | Microsoft Docs"
description: "Hur du använder funktionerna i ADAL SDK för att aktivera enkel inloggning i ditt program. "
services: active-directory
documentationcenter: 
author: danieldobalian
manager: mtillman
editor: 
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 04/07/2017
ms.author: dadobali
ms.custom: aaddev
ms.openlocfilehash: 7d832ecf3e9c64088a75cc88551879b4e09df715
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Aktivera enkel inloggning mellan appar på Android använder ADAL
Tillhandahåller enkel inloggning (SSO) så att användarna behöver bara ange sina autentiseringsuppgifter en gång och dessa autentiseringsuppgifter automatiskt fungerar över förväntade program nu av kunder. Svårt att ange sina användarnamn och lösenord på en liten skärm, ofta gånger kombineras med ytterligare en faktor (2FA) som ett telefonsamtal eller en textläge kod leder till att snabbt klagomål om en användare har att göra det mer än en gång till produkten.

Dessutom, om du använder en identity-plattform som andra program kan använda, till exempel Microsoft Accounts eller ett arbetskonto från Office365 kunder förväntar sig att de autentiseringsuppgifter som ska vara tillgängliga för användning i alla program oavsett leverantören.

Microsoft Identity-plattformen, tillsammans med vår Microsoft Identity-SDK: er, gör den här tunga arbetet åt dig och ger dig möjlighet att glädje dina kunder med enkel inloggning för antingen inom en egen uppsättning program eller, som i våra broker kapaciteten och autentiseraren program över hela enheten.

Den här genomgången kommer information om hur du konfigurerar våra SDK i ditt program att tillhandahålla förmånen till dina kunder.

Den här genomgången gäller för:

* Azure Active Directory
* Azure Active Directory B2C
* Azure Active Directory B2B
* Villkorsstyrd åtkomst med Azure Active Directory

Föregående dokumentet förutsätter att du vet hur du [etablera program i den äldra portalen för Azure Active Directory](active-directory-how-to-integrate.md) och integrerade programmet med den [Microsoft Identity Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>SSO begrepp i Microsoft Identity-plattformen
### <a name="microsoft-identity-brokers"></a>Microsoft Identity mäklare
Microsoft tillhandahåller program för varje mobil plattform som gör att för bryggning av autentiseringsuppgifter i program från olika leverantörer och gör för särskilda funktioner som kräver en enda säker plats varifrån att verifiera autentiseringsuppgifterna. Vi kallar dem **mäklare**. På iOS och Android tillhandahålls dessa via nedladdningsbara program att kunder installeras fristående eller pushas till enheten av ett företag som hanterar vissa eller alla av enheten för sina anställda. Dessa mäklare stöder Hantera säkerhet för vissa program eller hela enheten baserat på vad IT-administratörer som önskar. Den här funktionen tillhandahålls av en inbyggd i operativsystemet, kända tekniskt Webbautentiseringskoordinatorn väljare av användarkonto i Windows.

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
| Azure SDK  | | Azure SDK  |  | Azure SDK   |
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
 
 Behovet av att kontrollera identiteten för ett program anrop Service broker är avgörande för säkerheten som vi tillhandahåller i broker stödd inloggningar. Tillämpar unika identifierare är endast giltiga för ett visst program så att skadliga program kan ”förfalska” legitima program-ID och ta emot token som är avsedd för programmets legitima varken iOS eller Android. För att säkerställa att vi alltid kommunicerar med rätt programmet vid körning, ber vi utvecklare tillhandahålla anpassade redirectURI när de registrerar sina program med Microsoft. **Hur utvecklare ska använda för att skapa den här omdirigerings-URI diskuteras i detalj nedan.** Den här anpassade redirectURI innehåller certifikat-tumavtrycket för programmet och säkerställs unika tillämpningen av Google Play-butiken. När ett program anropar Service broker, frågar Service broker Android operativsystemet för att tillhandahålla tumavtrycket för certifikatet som kallas Service broker. Service broker innehåller det här tumavtrycket för certifikatet till Microsoft i anropet till vår identitetssystem. Om certifikat-tumavtrycket för programmet inte matchar tumavtrycket för certifikatet som angavs till oss av utvecklaren under registreringen, kommer vi neka åtkomst till token för den resurs som programmet begär. Försäkra dig om att det program som registrerats av utvecklaren tar emot tokens.

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
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
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
Vi använder här ADAL Android SDK, för att:

* Aktivera icke-förhandlad stödd enkel inloggning för en uppsättning appar
* Aktivera stöd för Service broker-stödd enkel inloggning

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Aktivera enkel inloggning för icke-förhandlad stödd SSO
För icke-förhandlad assisterad SSO över program hantera SDK: er för Microsoft Identity mycket komplex enkel inloggning för dig. Detta inkluderar att hitta rätt användaren i cachen och underhålla en lista över inloggade användare att fråga.

Att aktivera enkel inloggning för program som du äger måste du göra följande:

1. Se till att alla program användare samma klient-ID eller program-ID.
2. Kontrollera att alla program har samma uppsättning SharedUserID.
3. Kontrollera att alla dina program delar samma signeringscertifikat från Google Play store så att du kan dela lagring.

#### <a name="step-1-using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Steg 1: Använder samma klient-ID eller program-ID för alla program i din uppsättning appar
För Microsoft Identity-plattformen vet att den har tillåtelse för att dela token i dina program, måste var och en av dina program du dela samma klient-ID eller program-ID. Det här är den unika identifieraren som angavs för dig när du har registrerat din första program i portalen.

Du kanske undrar hur du identifierar olika appar till tjänsten Microsoft Identity om den använder den samma program-ID. Svaret är med i **omdirigerings-URI: er**. Varje program kan ha flera omdirigerings-URI: er registrerade i onboarding-portalen. Varje app i din suite har en annan omdirigerings-URI. Ett exempel på hur detta ser ut understiger:

App1 omdirigerings-URI:`msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D`

App2 omdirigerings-URI:`msauth://com.example.userapp1/KmB7PxIytyLkbGHuI%2UitkW%2Fejk%4E`

App3 omdirigerings-URI:`msauth://com.example.userapp2/Pt85PxIyvbLkbKUtBI%2SitkW%2Fejk%9F`

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

#### <a name="step-2-configuring-shared-storage-in-android"></a>Steg 2: Konfigurera delad lagring i Android
Ange den `SharedUserID` är utanför omfattningen för det här dokumentet, men kan läras in av dokumentationen Google Android på den [Manifest](http://developer.android.com/guide/topics/manifest/manifest-element.html). Vad är viktigt är att du bestämmer vad du vill att din sharedUserID anropas och använda det i alla program.

När du har den `SharedUserID` i dina program du är redo att använda enkel inloggning.

> [!WARNING]
> När du delar lagring över dina program kan alla program ta bort användare eller värre ta bort alla token för ditt program. Detta är särskilt katastrofal om du har program som förlitar sig på tokens för att background arbete. Dela lagring innebär att du måste vara mycket försiktig i alla remove-åtgärder via SDK: er för Microsoft Identity.
> 
> 

Klart! Microsoft Identity SDK kommer nu att dela autentiseringsuppgifter i alla program. Lista över användare också delas mellan programinstanser.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Aktivera enkel inloggning för broker stödd SSO
Möjligheten för ett program att använda alla broker som är installerad på enheten är **inaktiverad som standard**. För att kunna använda ditt program med Service broker måste du göra ytterligare konfigurering och Lägg till lite kod i ditt program.

Steg att följa är:

1. Aktivera Service broker-läge i din programkod anrop till MS-SDK
2. Upprätta en ny omdirigerings-URI och tillhandahålla som för både appen och appen registreringen
3. Konfigurera rätt behörighet i Android-manifest

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Steg 1: Aktivera Service broker-läge i ditt program
Möjligheten för programmet ska använda Service broker är aktiverat när du skapar ”inställningar” eller installationen av autentisering-instans. Du kan göra detta genom att ange ApplicationSettings-typ i koden:

```
AuthenticationSettings.Instance.setUseBroker(true);
```


#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Steg 2: Skapa en ny omdirigerings-URI med URL-schema
Vi behöver kontrollera vi kallar tillbaka till ditt program på ett sätt som operativsystemet Android kan kontrollera för att säkerställa att vi alltid returnera credential-token till rätt program. Android-operativsystemet använder hash för certifikatet i Google Play store. Detta kan vara falsk en otillåtna program. Därför kan utnyttja vi detta tillsammans med URI för appen broker för att säkerställa att token som returneras till rätt program. Vi måste du upprätta detta unika omdirigerings-URI för både i ditt program och ange som en omdirigerings-URI i vår developer-portalen.

Omdirigerings-URI måste vara i rätt form av:

`msauth://packagename/Base64UrlencodedSignature`

ex: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Den här omdirigerings-URI måste anges i din app registrering med hjälp av den [Azure-portalen](https://portal.azure.com/). Mer information om registrering av Azure AD app finns [integrera med Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Steg 3: Ställ in behörighet i ditt program
Vårt broker program i Android använder funktionen Accounts Manager i Android OS för att hantera autentiseringsuppgifter i program. För att kunna använda Service broker i Android måste app-manifest ha behörighet att använda AccountManager konton. Det beskrivs i detalj i den [Google-dokumentationen för hanteraren för kontosäkerhet här](http://developer.android.com/reference/android/accounts/AccountManager.html)

I synnerhet är dessa behörigheter:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Du har konfigurerat SSO!
Nu Microsoft Identity SDK automatiskt både dela autentiseringsuppgifter i dina program och anropa Service broker om den finns på sin enhet.

