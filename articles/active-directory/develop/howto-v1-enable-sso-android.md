---
title: Så här aktiverar du enkel inloggning mellan appar på Android med hjälp av ADAL | Microsoft Docs
description: Hur du använder funktionerna i ADAL SDK för smidig enkel inloggning i dina program.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5085acad8a82394340892a3a67a3d1e5d85384b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60410373"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Anvisningar: Aktivera enkel inloggning mellan appar på Android använder ADAL

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Enkel inloggning (SSO) kan användarna bara ange sina autentiseringsuppgifter för en gång och autentiseringsuppgifterna fungera automatiskt mellan program och plattformar som andra program kan använda (till exempel Microsoft Accounts eller ett arbetskonto från Microsoft 365) inga utgivaren är viktiga för dig.

Microsofts identitetsplattform, tillsammans med SDK: er, gör det enkelt att aktivera enkel inloggning i din egen suite appar eller med broker funktions- och Authenticator-program, över hela enheten.

I den här anvisningen lär du dig att konfigurera SDK i ditt program att erbjuda enkel inloggning till dina kunder.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här anvisningen förutsätter att du vet hur du:

- Etablera din app med den äldre portalen för Azure Active Directory (AD Azure). Mer information finns i [registrera en app med Azure AD v1.0-slutpunkten](quickstart-v1-add-azure-ad-app.md)
- Integrera programmet med den [Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Begrepp för enkel inloggning

### <a name="identity-brokers"></a>Identitet asynkrona meddelandeköer

Microsoft tillhandahåller program för alla mobila plattformar som möjliggör överbryggning av autentiseringsuppgifter i program från olika leverantörer och förbättrade funktioner som kräver en enda säker plats varifrån att verifiera autentiseringsuppgifterna. Dessa kallas **mäklare**.

På iOS och Android tillhandahålls Broker-programmen genom nedladdningsbara program att kunder installeras fristående eller skickas till enheten av ett företag som hanterar vissa eller alla, av enheterna som för anställda. Stöd för asynkrona Meddelandeköer Hantera säkerhet för vissa program eller hela enheten baserat på konfigurationen för IT-administratören. Den här funktionen tillhandahålls i Windows, en kontoväljare som är inbyggda i operativsystemet, som Webbautentiseringskoordinatorn.

#### <a name="broker-assisted-login"></a>Assisterad Broker-inloggning

Broker-assisted inloggningar är inloggningen upplevelser som inträffar inom broker-programmet och Använd storage och säkerheten för den asynkrona meddelandekön för att dela autentiseringsuppgifter för alla program på enheten som gäller identity-plattformen. Konsekvenserna som dina program förlitar sig på den asynkrona meddelandekön för inloggning av användare. På iOS och Android tillhandahålls Broker-programmen via nedladdningsbara program att kunder installeras fristående eller kan skickas till enheten av ett företag som hanterar enheten för sina användare. Ett exempel på den här typen av program är Microsoft Authenticator-program på iOS. Den här funktionen tillhandahålls i Windows, en kontoväljare som är inbyggda i operativsystemet, som Webbautentiseringskoordinatorn.
Upplevelsen varierar efter plattform och kan ibland vara störande för användarna om den inte hanteras korrekt. Du är förmodligen mest bekant med det här mönstret om du har installerat Facebook-program och använder Facebook ansluta från ett annat program. Identity-plattformen använder samma mönster.

På Android visas kontoväljare ovanpå ditt program, vilket är mindre störande för användaren.

#### <a name="how-the-broker-gets-invoked"></a>Hur den asynkrona meddelandekön hämtar anropas

Om en kompatibel broker är installerad på enheten, t.ex. Microsoft Authenticator-programmet identiteten SDK: er automatiskt gör verk som tillhör anropar den asynkrona meddelandekön åt dig när en användare anger de vill logga in med ett konto från identity-plattformen.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Hur Microsoft garanterar programmet är giltigt

Behovet av att se till att identiteten för ett program anropa den asynkrona meddelandekön är avgörande för den säkerhet som tillhandahålls i broker assisted-inloggningar. iOS och Android påtvingar inte unika identifierare som är bara giltiga för ett visst program så att skadliga program kan ”förfalska” legitima program-ID och ta emot de token som är avsedd för legitima programmet. För att säkerställa Microsoft kommunicerar alltid med rätt program under körning, uppmanas utvecklare att tillhandahålla en anpassad redirectURI när de registrerar sina program med Microsoft. **Hur utvecklare ska använda för att skapa den här omdirigerings-URI beskrivs i detalj nedan.** Den här anpassade redirectURI innehåller certifikat-tumavtrycket för programmet och säkerställs för att vara unikt för programmet genom att Google Play Store. När ett program anropar den asynkrona meddelandekön, frågar den asynkrona meddelandekön Android operativsystemet för att tillhandahålla tumavtrycket för certifikatet som kallas den asynkrona meddelandekön. Den asynkrona meddelandekön tillhandahåller det här certifikattumavtrycket till Microsoft i anropet till ID-system. Om certifikat-tumavtrycket för programmet inte matchar tumavtrycket för certifikatet som tillhandahålls till oss av utvecklaren under registreringen, nekas åtkomst till token för den resurs som programmet begär. Den här kontrollen säkerställer att endast program som registrerats av utvecklaren får token.

Asynkrona SSO inloggningar har följande fördelar:

* SSO användarupplevelser i alla sina program oavsett leverantören.
* Ditt program kan använda mer avancerade funktioner, till exempel villkorlig åtkomst och ge stöd för Intune-scenarier.
* Ditt program har stöd för certifikatbaserad autentisering för företagsanvändare.
* Säkrare inloggning som identitet för programmet och användaren kan verifieras genom broker-program med algoritmer för ytterligare säkerhet och kryptering.

Här är en representation av hur de SDK: er fungerar med broker-program för att aktivera enkel inloggning:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
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

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Aktivera enkel inloggning för broker assisted SSO

Möjligheten för ett program att använda alla broker som är installerad på enheten är inaktiverat som standard. För att kunna använda ditt program med den asynkrona meddelandekön, måste du göra ytterligare konfigurering och lägga till kod i ditt program.

Steg att följa är:

1. Aktivera broker läge i din programkod anropa till MS-SDK
2. Upprätta en ny omdirigerings-URI och ger som för både appen och appen registreringen
3. Ställa in behörighet i Android-manifestet

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Steg 1: Aktivera broker läge i ditt program

Funktioner som gör att programmet ska använda den asynkrona meddelandekön aktiveras när du skapar ”inställningar” eller den initiala konfigurationen av din instans av autentisering. Att göra det i din app:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Steg 2: Upprätta en ny omdirigerings-URI med URL-schema

För att säkerställa att rätt programmet tar emot den returnerade autentiseringsuppgifterna tokens, det är nödvändigt att kontrollera att anropet tillbaka till programmet på ett sätt som operativsystemet Android kan verifiera. Android-operativsystemet använder hash för certifikatet i Google Play-butiken. Den här hash för certifikatet kan inte förfalskas av en otillåtna program. Microsoft garanterar att token som returneras till rätt program tillsammans med URI: N för broker-programmet. En unik omdirigerings-URI krävs som ska registreras i programmet.

Omdirigerings-URI måste vara i rätt format för:

`msauth://packagename/Base64UrlencodedSignature`

ex: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Du kan registrera den här omdirigerings-URI i din app registrering med den [Azure-portalen](https://portal.azure.com/). Mer information om Azure AD app-registrering finns i [integrera med Azure Active Directory](active-directory-how-to-integrate.md).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Steg 3: Konfigurera rätt behörigheter i ditt program

Broker-programmet i Android använder Accounts Manager-funktion i Android OS för att hantera autentiseringsuppgifter i program. För att kunna använda den asynkrona meddelandekön i Android måste ditt appmanifest ha behörighet att använda AccountManager-konton. Dessa behörigheter beskrivs i detalj i de [Google dokumentationen för Account Manager](https://developer.android.com/reference/android/accounts/AccountManager.html)

I synnerhet är de här behörigheterna:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Du har konfigurerat SSO!

Nu identiteten SDK: N automatiskt både dela autentiseringsuppgifter i dina program och anropar den asynkrona meddelandekön om den finns på enheten.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [enkel inloggning SAML-protokoll](single-sign-on-saml-protocol.md)
