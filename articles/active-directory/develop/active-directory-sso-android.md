---
title: Så här aktiverar du enkel inloggning mellan appar på Android med hjälp av ADAL | Microsoft Docs
description: 'Hur du använder funktionerna i ADAL SDK för att aktivera enkel inloggning i ditt program. '
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 06/13/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 4abf6bd2d82753e22d4fde92e219109274ce36be
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37436148"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Så här aktiverar du enkel inloggning mellan appar på Android använder ADAL
Att tillhandahålla enkel inloggning (SSO) så att användarna behöver bara ange sina autentiseringsuppgifter på en gång och har de autentiseringsuppgifterna som automatiskt fungerar på olika program är nu en branschstandard. Svårigheterna med att ange sitt användarnamn och lösenord på en liten skärm, ofta gånger i kombination med ytterligare en faktor (2FA) som ett telefonsamtal eller en textläge kod, resulterar i klagomål från slutanvändarna om en användare har logga in mer än en gång.

Dessutom, om du använder en identity-plattformen som andra program kan använda, till exempel Microsoft Accounts eller ett arbetskonto från Microsoft365 kunderna förväntar sig att de autentiseringsuppgifter som ska vara tillgängliga för användning i alla sina program oavsett utgivaren.

Microsoft Identity-plattformen, tillsammans med SDK: er för Microsoft Identity ger dig möjlighet att fler nöjda kunder med enkel inloggning antingen inom en egen uppsättning program eller, precis som med broker funktions- och Authenticator-program, över hela enheten.

Den här genomgången kommer information om hur du konfigurerar SDK i ditt program att erbjuda enkel inloggning till dina kunder.

Föregående dokumentet förutsätter att du vet hur du integrerar dina program med den [Android SDK för Microsoft Identity](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="sso-concepts-in-the-microsoft-identity-platform"></a>SSO-begrepp i Microsoft Identity-plattformen
### <a name="microsoft-identity-brokers"></a>Microsoft Identity Brokers
Microsoft tillhandahåller program för alla mobila plattformar som ska ge överbryggning av autentiseringsuppgifter i program från olika leverantörer och tillåter specialfunktioner som kräver en enda säker plats varifrån att verifiera autentiseringsuppgifterna. SDK anropar dessa **mäklare**. På iOS och Android tillhandahålls de asynkrona Meddelandeköer genom nedladdningsbart program att kunder installeras fristående eller kan skickas till enheten av ett företag som hanterar vissa eller samtliga av enheten för sina anställda. Broker-programmen stöd Hantera säkerhet för vissa program eller hela enheten baserat på vad IT-administratörer vill ha. Den här funktionen tillhandahålls i Windows, en kontoväljare som är inbyggda i operativsystemet, som Webbautentiseringskoordinatorn.

#### <a name="broker-assisted-logins"></a>Mäkla assisterad inloggningar
Broker-assisted inloggningar är inloggningen upplevelser som inträffar inom broker-programmet och Använd storage och säkerheten för den asynkrona meddelandekön för att dela autentiseringsuppgifter för alla program på enheten som gäller Microsoft Identity-plattformen. Konsekvenserna som dina program förlitar sig på den asynkrona meddelandekön för inloggning av användare. På iOS och Android tillhandahålls Broker-programmen via nedladdningsbara program att kunder installeras fristående eller kan skickas till enheten av ett företag som hanterar enheten för sina användare. Ett exempel på den här typen av program är Microsoft Authenticator-program på iOS. Den här funktionen tillhandahålls i Windows, en kontoväljare som är inbyggda i operativsystemet, som Webbautentiseringskoordinatorn.
Upplevelsen varierar efter plattform och kan ibland vara störande för användarna om den inte hanteras korrekt. Du är förmodligen mest bekant med det här mönstret om du har installerat Facebook-program och använder Facebook ansluta från ett annat program. Microsoft Identity-plattformen använder samma mönster.

På Android visas kontoväljare ovanpå ditt program, vilket är mindre störande för användaren.

#### <a name="how-the-broker-gets-invoked"></a>Hur den asynkrona meddelandekön hämtar anropas
Om en kompatibel broker är installerad på enheten, t.ex. Microsoft Authenticator-program, gör Microsoft Identity-SDK: er automatiskt verk som tillhör anropar den asynkrona meddelandekön åt dig när en användare anger de vill logga in med ett konto från Microsoft Identity-plattformen. 
 
 #### <a name="how-microsoft-ensures-the-application-is-valid"></a>Hur Microsoft garanterar programmet är giltigt
 
 Behovet av att se till att identiteten för ett program anropa den asynkrona meddelandekön är avgörande för den säkerhet som tillhandahålls i broker assisted-inloggningar. iOS och Android påtvingar inte unika identifierare som är bara giltiga för ett visst program så att skadliga program kan ”förfalska” legitima program-ID och ta emot de token som är avsedd för legitima programmet. För att säkerställa Microsoft kommunicerar alltid med rätt program under körning, uppmanas utvecklare att tillhandahålla en anpassad redirectURI när de registrerar sina program med Microsoft. **Hur utvecklare ska använda för att skapa den här omdirigerings-URI beskrivs i detalj nedan.** Den här anpassade redirectURI innehåller certifikat-tumavtrycket för programmet och säkerställs för att vara unikt för programmet genom att Google Play Store. När ett program anropar den asynkrona meddelandekön, frågar den asynkrona meddelandekön Android operativsystemet för att tillhandahålla tumavtrycket för certifikatet som kallas den asynkrona meddelandekön. Den asynkrona meddelandekön tillhandahåller det här certifikattumavtrycket till Microsoft i anropet till ID-system. Om certifikat-tumavtrycket för programmet inte matchar tumavtrycket för certifikatet som tillhandahålls till oss av utvecklaren under registreringen, nekas åtkomst till token för den resurs som programmet begär. Den här kontrollen säkerställer att endast program som registrerats av utvecklaren får token.

Asynkrona SSO inloggningar har följande fördelar:

* SSO användarupplevelser i alla sina program oavsett leverantören.
* Ditt program kan använda mer avancerade funktioner, till exempel villkorlig åtkomst och ge stöd för Intune-scenarier.
* Ditt program har stöd för certifikatbaserad autentisering för företagsanvändare.
* Säkrare inloggning som identitet för programmet och användaren kan verifieras genom broker-program med algoritmer för ytterligare säkerhet och kryptering.

Här är en representation av hur Microsoft Identity-SDK: er fungerar med broker-program för att aktivera enkel inloggning:

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

Som enda verktyg med den här bakgrundsinformation du borde att bättre förstå och implementera enkel inloggning i ditt program med hjälp av Microsoft Identity-plattformen och SDK: er.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Aktivera enkel inloggning för broker assisted SSO
Möjligheten för ett program att använda alla broker som är installerad på enheten är **inaktiverat som standard**. För att kunna använda ditt program med den asynkrona meddelandekön, måste du göra ytterligare konfigurering och lägga till kod i ditt program.

Steg att följa är:

1. Aktivera broker läge i din programkod anropa till MS-SDK
2. Upprätta en ny omdirigerings-URI och ger som för både appen och appen registreringen
3. Ställa in behörighet i Android-manifestet

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Steg 1: Aktivera broker läge i ditt program
Funktioner som gör att programmet ska använda den asynkrona meddelandekön aktiveras när du skapar ”inställningar” eller den initiala konfigurationen av din instans av autentisering. Att göra det i din app:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Steg 2: Skapa en ny omdirigerings-URI med URL-schema
För att säkerställa att rätt program recevies den returnerade autentiseringsuppgifterna tokens, det är nödvändigt att kontrollera att anropet tillbaka till programmet på ett sätt som operativsystemet Android kan verifiera. Android-operativsystemet använder hash för certifikatet i Google Play-butiken. Den här hash för certifikatet kan inte förfalskas av en otillåtna program. Microsoft garanterar att token som returneras till rätt program tillsammans med URI: N för broker-programmet. En unik omdirigerings-URI krävs som ska registreras i programmet.

Omdirigerings-URI måste vara i rätt format för:

`msauth://packagename/Base64UrlencodedSignature`

Exempel: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Du kan registrera den här omdirigerings-URI i din app registrering med den [Azure-portalen](https://portal.azure.com/). Mer information om Azure AD app-registrering finns i [integrera med Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Steg 3: Konfigurera rätt behörigheter i ditt program
Broker-programmet i Android använder Accounts Manager-funktion i Android OS för att hantera autentiseringsuppgifter i program. För att kunna använda den asynkrona meddelandekön i Android måste ditt appmanifest ha behörighet att använda AccountManager-konton. Dessa behörigheter beskrivs i detalj i de [Google dokumentationen för Account Manager](http://developer.android.com/reference/android/accounts/AccountManager.html)

I synnerhet är de här behörigheterna:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Du har konfigurerat SSO!
Nu Microsoft Identity-SDK: N automatiskt både dela autentiseringsuppgifter i dina program och anropar den asynkrona meddelandekön om den finns på enheten.

