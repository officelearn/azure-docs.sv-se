---
title: 'Mobilapp som anropar webb-API: er – appens kod konfiguration | Microsoft Identity Platform'
description: 'Lär dig hur du skapar en mobilapp som anropar webb-API: er (appens kod konfiguration)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9bebaa5d35876d562e567a8398cc7a9ce7e6f488
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413593"
---
# <a name="mobile-app-that-calls-web-apis---app-registration"></a>Mobilapp som anropar webb-API: er – app-registrering

Den här artikeln innehåller anvisningar för appens registrering för att skapa ett mobil program.

## <a name="supported-accounts-types"></a>Typer av konton som stöds

De konto typer som stöds i mobila program beror på vilken upplevelse du vill aktivera och vilka flöden du vill använda.

### <a name="audience-for-interactive-token-acquisition"></a>Mål grupp för hämtning av interaktiva token

De flesta mobila program använder interaktiv autentisering. I så fall kan du logga in användare från vilken [Kontotyp](quickstart-register-app.md#register-a-new-application-using-the-azure-portal) som helst

### <a name="audience-for-integrated-authentication-usernamepassword-and-b2c"></a>Mål grupp för integrerad autentisering, användar namn/lösen ord och B2C

- Om du avser att använda integrerad Windows-autentisering (möjligt i UWP-appar) eller användar namn/lösen ord måste ditt program logga in användare i din egen klient organisation (LOB-utvecklare) eller i Azure Active Directory-organisationer (ISV-scenario). Dessa autentiserings flöden stöds inte för Microsoft-personliga konton
- Om du loggar in användare med sociala identiteter som skickar en B2C-auktoritet och-princip kan du bara använda interaktiv och username-Password Authentication. Username-Password stöds för närvarande endast på Xamarin. iOS, Xamarin. Android och UWP.

För den stora bilden, se [scenarier och stödda autentiseringsscheman](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) och [scenarier och plattformar och språk som stöds](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="platform-configuration-and-redirect-uris"></a>Plattforms konfiguration och omdirigering av URI: er  

### <a name="interactive-authentication"></a>Interaktiv autentisering

När du skapar en mobilapp med interaktiv autentisering är den mest kritiska registreringen den omdirigerings-URI: n. Detta kan ställas in via [plattforms konfigurationen på bladet autentisering](https://aka.ms/MobileAppReg).

Den här funktionen gör det möjligt för din app att få enkel inloggning (SSO) via Microsoft Authenticator (och Intune-företagsportal på Android) samt stöd för enhets hanterings principer.

Observera att det finns en förhands gransknings upplevelse i appens registrerings portal som hjälper dig att beräkna den asynkrona svars-URI: n för iOS-och Android-program:

1. I appens registrering väljer du **autentisering** och urval **testa den nya upplevelse**
   ![avbildningen](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Välj **Lägg till plattforms**
   ![avbildning](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. När listan över plattformar stöds väljer du **iOS**
   ![-avbildning](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Ange ditt paket-ID enligt begäran och tryck sedan på **Registrera**
   ![avbildning](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. Omdirigerings-URI: n beräknas för dig.
   ![avbildning](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Om du föredrar att konfigurera omdirigerings-URI: n manuellt kan du göra det via applikations manifestet. Det rekommenderade formatet är följande:

- ***iOS***: `msauth.<BUNDLE_ID>://auth` (för instans "msauth. com. yourcompany. APPNAME://auth")
- ***Android***: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Hash-signaturens hash kan genereras med hjälp av versions-eller fel söknings nycklarna via kommando kommandot.

### <a name="username-password"></a>Användar namn lösen ord

Om din app endast använder användar namn/lösen ord, behöver du inte registrera en omdirigerings-URI för programmet. Det här flödet gör en tur och retur till Microsoft Identity Platform v 2.0-slutpunkten och programmet kommer inte att anropas tillbaka på någon specifik URI. Du måste dock uttrycka att ditt program är ett offentligt klient program. Den här konfigurationen uppnås genom att gå  till avsnittet Authentication för ditt program. i underavsnittet **Avancerade inställningar** väljer du **Ja**för frågan **behandla programmet som en offentlig klient** (i **standard klient typ** stycke)

## <a name="api-permissions"></a>API-behörigheter

Mobil program anropar API: er å den inloggade användarens vägnar. Appen måste begära delegerade behörigheter, även kallat omfång. Beroende på den önskade upplevelsen kan detta göras statiskt via Azure Portal eller dynamiskt vid körning. Med tillstånd för att registrera statiskt kan administratörer enkelt godkänna din app och rekommenderas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kod konfiguration](scenario-mobile-app-configuration.md)
