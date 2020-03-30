---
title: Så här aktiverar du SSO över flera appar på Android med ADAL | Microsoft-dokument
description: Så här använder du funktionerna i ADAL SDK för att aktivera enkel inloggning i dina program.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 0b87a9cd0ae29281faad4209f4449d547921835d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154822"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Så här aktiverar du SSO över flera appar på Android med ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Med enkel inloggning (SSO) kan användare bara ange sina autentiseringsuppgifter en gång och låta dessa autentiseringsuppgifter automatiskt arbeta mellan program och på olika plattformar som andra program kan använda (till exempel Microsoft-konton eller ett arbetskonto från Microsoft 365) fråga förlaget.

Microsofts identitetsplattform, tillsammans med SDK:erna, gör det enkelt att aktivera SSO i din egen serie av appar, eller med mäklarfunktionen och Authenticator-programmen, över hela enheten.

I det här programmet får du lära dig hur du konfigurerar SDK i programmet så att SSO får till dina kunder.

## <a name="prerequisites"></a>Krav

Detta instruktioner förutsätter att du vet hur man:

- Etablera din app med den äldre portalen för Azure Active Directory (Azure AD). Mer information finns i [Registrera en app](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
- Integrera ditt program med [Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Enstaka inloggningsbegrepp

### <a name="identity-brokers"></a>Identitetsmäklare

Microsoft tillhandahåller program för varje mobil plattform som möjliggör överbryggande av autentiseringsuppgifter mellan program från olika leverantörer och för förbättrade funktioner som kräver en enda säker plats varifrån autentiseringsuppgifterna kan valideras. Dessa kallas **mäklare**.

På iOS och Android tillhandahålls mäklare via nedladdningsbara program som kunder antingen installerar självständigt eller skjuts till enheten av ett företag som hanterar vissa, eller alla, enheter för sina anställda. Mäklare stöder hantering av säkerhet bara för vissa program eller hela enheten baserat på IT-administratörskonfiguration. I Windows tillhandahålls den här funktionen av en kontoväljare inbyggd i operativsystemet, tekniskt känd som Webbautentiseringsmäklaren.

#### <a name="broker-assisted-login"></a>Mäklare assisterad inloggning

Broker-assisted inloggningar är inloggningsupplevelser som uppstår inom mäklaren ansökan och använda lagring och säkerhet för mäklaren att dela autentiseringsuppgifter över alla program på enheten som tillämpar identitetsplattformen. Innebörden är dina program kommer att förlita sig på mäklaren att logga in användare. På iOS och Android tillhandahålls dessa mäklare via nedladdningsbara program som kunder antingen installerar självständigt eller kan skickas till enheten av ett företag som hanterar enheten för sin användare. Ett exempel på den här typen av program är Microsoft Authenticator-programmet på iOS. I Windows tillhandahålls den här funktionen av en kontoväljare inbyggd i operativsystemet, tekniskt känd som Webbautentiseringsmäklaren.
Upplevelsen varierar beroende på plattform och kan ibland vara störande för användarna om den inte hanteras korrekt. Du är förmodligen mest bekant med detta mönster om du har Facebook-programmet installerat och använda Facebook Connect från ett annat program. Identitetsplattformen använder samma mönster.

På Android visas kontoväljaren ovanpå ditt program, vilket är mindre störande för användaren.

#### <a name="how-the-broker-gets-invoked"></a>Hur mäklaren blir åberopad

Om en kompatibel mäklare är installerad på enheten, som Microsoft Authenticator-programmet, kommer identitets-SDK:erna automatiskt att göra jobbet för att anropa mäklaren åt dig när en användare anger att de vill logga in med ett konto från identitetsplattformen.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Så här säkerställer Microsoft att programmet är giltigt

Behovet av att säkerställa identiteten på ett program som ringer mäklaren är avgörande för säkerheten i mäklare assisterade inloggningar. iOS och Android framtvingar inte unika identifierare som är giltiga endast för ett visst program, så skadliga program kan "förfalska" en legitim programs identifierare och ta emot de token som är avsedda för det legitima programmet. För att säkerställa att Microsoft alltid kommunicerar med rätt program vid körning ombeds utvecklaren att tillhandahålla en anpassad redirectURI när de registrerar sitt program hos Microsoft. **Hur utvecklare ska skapa den här omdirigera URI:n diskuteras i detalj nedan.** Denna anpassade redirectURI innehåller programmets certifikattumavtryck och säkerställs att det är unikt för programmet av Google Play Store. När ett program anropar mäklaren ber mäklaren Operativsystemet Android att förse det med certifikatets tumavtryck som kallas mäklaren. Mäklaren tillhandahåller det här certifikatets tumavtryck till Microsoft i anropet till identitetssystemet. Om certifikatets tumavtryck för programmet inte matchar certifikatets tumavtryck som tillhandahålls oss av utvecklaren under registreringen nekas åtkomst till token för den resurs som programmet begär. Den här kontrollen säkerställer att endast det program som registrerats av utvecklaren tar emot token.

Förmedlade SSO-inloggningar har följande fördelar:

* Användarupplevelser SSO i alla sina program oavsett leverantör.
* Ditt program kan använda mer avancerade affärsfunktioner som villkorlig åtkomst och stödja Intune-scenarier.
* Ditt program kan stödja certifikatbaserad autentisering för företagsanvändare.
* Säkrare inloggningsupplevelse som programmets identitet och användaren verifieras av mäklarprogrammet med ytterligare säkerhetsalgoritmer och kryptering.

Här är en representation av hur SDK fungerar med mäklaren applikationer för att aktivera SSO:

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

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Aktivera SSO för mäklare assisterad SSO

Möjligheten för ett program att använda alla mäklare som är installerade på enheten är inaktiverad som standard. För att kunna använda ditt program med mäklaren måste du göra ytterligare konfiguration och lägga till lite kod till ditt program.

Stegen att följa är:

1. Aktivera mäklarläge i programkodens anrop till MS SDK
2. Upprätta en ny omdirigera URI och ge det till både appen och din appregistrering
3. Ställa in rätt behörigheter i Android-manifestet

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Steg 1: Aktivera mäklarläge i ditt program

Möjligheten för ditt program att använda mäklaren är aktiverad när du skapar "inställningar" eller första installationen av din autentiseringsinstans. Så här gör du i appen:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Steg 2: Upprätta en ny omdirigera URI med ditt URL-schema

För att säkerställa att rätt program tar emot de returnerade autentiseringstoken, finns det ett behov av att se till att samtalet tillbaka till ditt program på ett sätt som Operativsystemet Android kan verifiera. Operativsystemet Android använder hash-värdet för certifikatet i Google Play Store. Detta hash-certifikat kan inte förfalskas av en otillåten ansökan. Tillsammans med URI för mäklarprogrammet ser Microsoft till att token returneras till rätt program. En unik omdirigerings-URI måste registreras i programmet.

Din omdirigera URI måste vara i rätt form av:

`msauth://packagename/Base64UrlencodedSignature`

ex: *msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Du kan registrera den här omdirigerings-URI:n i appregistreringen med [Azure-portalen](https://portal.azure.com/). Mer information om registrering av Azure AD-appar finns i [Integrera med Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Steg 3: Konfigurera rätt behörigheter i ditt program

Mäklarprogrammet i Android använder funktionen Accounts Manager i Android OS för att hantera autentiseringsuppgifter över program. För att kunna använda mäklaren i Android måste ditt appmanifest ha behörighet att använda AccountManager-konton. Dessa behörigheter beskrivs i detalj i [Googles dokumentation för Account Manager här](https://developer.android.com/reference/android/accounts/AccountManager.html)

Dessa behörigheter är särskilt följande:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Du har konfigurerat SSO!

Nu kommer identiteten SDK automatiskt både dela autentiseringsuppgifter över dina program och anropa mäklaren om det finns på deras enhet.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [SAML-protokoll för enkel inloggning](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
