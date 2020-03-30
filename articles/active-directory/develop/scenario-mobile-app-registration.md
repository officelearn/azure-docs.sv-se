---
title: Registrera mobilappar som anropar webb-API:er | Azure
titleSuffix: Microsoft identity platform
description: Lär dig hur du skapar en mobilapp som anropar webb-API:er (appens kodkonfiguration)
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
ms.openlocfilehash: 75cfd304869bfb63131dfd2afed9f925c86d32fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132443"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Registrera mobilappar som anropar webb-API:er

Den här artikeln innehåller instruktioner som hjälper dig att registrera ett mobilprogram som du skapar.

## <a name="supported-account-types"></a>Kontotyper som stöds

Vilka kontotyper som dina mobilappar stöder beror på den upplevelse som du vill aktivera och vilka flöden du vill använda.

### <a name="audience-for-interactive-token-acquisition"></a>Målgrupp för interaktivt tokenförvärv

De flesta mobila program använder interaktiv autentisering. Om din app använder den här formen av autentisering kan du logga in användare från valfri [kontotyp](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>Målgrupp för integrerad Windows-autentisering, användarnamn och B2C

Om du har en UWP-app (Universal Windows Platform) kan du använda integrerad Windows-autentisering för att logga in användare. Om du vill använda autentisering av integrerad Windows-autentisering eller autentisering av användarnamn och lösenord måste programmet logga in användare i din egen LOB-utvecklares klientorganisation. I ett oberoende ISV-scenario (Software Vendor) kan ditt program logga in användare i Azure Active Directory-organisationer. Dessa autentiseringsflöden stöds inte för Microsofts personliga konton.

Du kan också logga in användare med hjälp av sociala identiteter som skickar en B2C-auktoritet och princip. Om du vill använda den här metoden kan du bara använda interaktiv autentisering och autentisering av användarnamn och lösenord. Autentisering av användarnamn och lösenord stöds för närvarande endast på Xamarin.iOS, Xamarin.Android och UWP.

Mer information finns i [Scenarier och autentiseringsflöden](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows) och [scenarier och plattformar och språk som stöds](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages).

## <a name="platform-configuration-and-redirect-uris"></a>Plattformskonfiguration och omdirigera URI:er  

### <a name="interactive-authentication"></a>Interaktiv autentisering

När du skapar en mobilapp som använder interaktiv autentisering är det mest kritiska registreringssteget omdirigerings-URI.When you build a mobile app that uses interactive authentication, the most critical registration step is the redirect URI. Du kan ställa in interaktiv autentisering via [plattformskonfigurationen på **autentiseringsbladet** ](https://aka.ms/MobileAppReg).

Den här upplevelsen gör det möjligt för din app att få enkel inloggning (SSO) via Microsoft Authenticator (och Intune Company Portal på Android). Det kommer också att stödja enhetshanteringsprinciper.

Appregistreringsportalen ger en förhandsgranskningsupplevelse som hjälper dig att beräkna den förmedlade svars-URI:n för iOS- och Android-program:

1. I appregistreringsportalen väljer du **Autentisering** > **Prova den nya upplevelsen**.

   ![Autentiseringsbladet, där du väljer en ny upplevelse](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Välj **Lägg till en plattform**.

   ![Lägga till en plattform](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. När listan över plattformar stöds väljer du **iOS**.

   ![Välj en mobil applikation](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Ange ditt bunt-ID och välj sedan **Registrera**.

   ![Ange ditt bunt-ID](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

När du slutför stegen beräknas omdirigerings-URI:n åt dig, som i följande avbildning.

![Den resulterande omdirigerings-URI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

Om du föredrar att manuellt konfigurera omdirigerings-URI:n kan du göra det via programmanifestet. Här är det rekommenderade formatet för manifestet:

- **iOS**:`msauth.<BUNDLE_ID>://auth` 
  - Ange till exempel`msauth.com.yourcompany.appName://auth`
- **Android**:`msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Du kan generera Android signaturhhh genom att använda releasenyckeln eller felsökningsnyckeln via KeyTool-kommandot.

### <a name="username-password-authentication"></a>Autentisering av användarnamn och lösenord

Om din app bara använder autentisering av användarnamn och lösenord behöver du inte registrera en omdirigerings-URI för ditt program. Det här flödet gör en rundresa till Microsoft identity platform version 2.0-slutpunkten. Ditt program anropas inte på någon specifik URI. 

Du måste dock identifiera ditt program som ett offentligt klientprogram. Det gör du genom att starta i avsnittet **Autentisering** i programmet. I underavsnittet **Avancerade inställningar** i stycket **Standardklienttyp** väljer du **Ja**för **frågebehandla programmet Behandla som en offentlig klient**.

## <a name="api-permissions"></a>API-behörigheter

Mobila program anropar API:er för den inloggade användarens räkning. Din app måste begära delegerade behörigheter. Dessa behörigheter kallas också scope. Beroende på vilken erfarenhet du vill ha kan du begära delegerade behörigheter statiskt via Azure-portalen. Eller så kan du begära dem dynamiskt vid körning. 

Genom att statiskt registrera behörigheter tillåter du administratörer att enkelt godkänna din app. Statisk registrering rekommenderas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfiguration av kod](scenario-mobile-app-configuration.md)
