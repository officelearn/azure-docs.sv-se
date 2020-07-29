---
title: 'Registrera skrivbordsappar som anropar webb-API: er – Microsoft Identity Platform | Azure'
description: 'Lär dig hur du skapar en stationär app som anropar webb-API: er (app-registrering)'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 599603ba867e21694392e38e9692280f010e08eb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885165"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Skriv bords app som anropar webb-API: registrera appar

Den här artikeln beskriver app-registreringen av specifika program för ett Skriv bords program.

## <a name="supported-account-types"></a>Kontotyper som stöds

De konto typer som stöds i ett Skriv bords program beror på vilken upplevelse du vill se. På grund av den här relationen är de konto typer som stöds beroende av de flöden som du vill använda.

### <a name="audience-for-interactive-token-acquisition"></a>Mål grupp för hämtning av interaktiva token

Om ditt Skriv bords program använder interaktiv autentisering kan du logga in användare från vilken [Kontotyp](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)som helst.

### <a name="audience-for-desktop-app-silent-flows"></a>Mål grupp för tyst flöden i Desktop-appen

- Om du vill använda integrerad Windows-autentisering eller ett användar namn och ett lösen ord måste ditt program logga in användare i din egen klient, till exempel om du är en affärsutvecklare (LOB). Eller, i Azure Active Directory organisationer, måste ditt program logga in användare i din egen klient om det är ett ISV-scenario. Dessa autentiserings flöden stöds inte för Microsoft-personliga konton.
- Om du vill använda enhets kod flödet kan du inte logga in användare med sina personliga Microsoft-konton ännu.
- Om du loggar in användare med sociala identiteter som skickar en B2C-auktoritet (Business-to-Commerce) och-princip kan du bara använda autentiseringen interaktiv och username-Password.

## <a name="redirect-uris"></a>Omdirigera URI: er

De omdirigerings-URI: er som ska användas i ett Skriv bords program beror på det flöde som du vill använda.

- Om du använder interaktiv autentisering eller enhets kod flöde använder du `https://login.microsoftonline.com/common/oauth2/nativeclient` . Välj motsvarande URL i avsnittet **autentisering** för ditt program för att uppnå den här konfigurationen.
  
  > [!IMPORTANT]
  > Idag använder MSAL.NET en annan omdirigerings-URI som standard i Skriv bords program som körs på Windows ( `urn:ietf:wg:oauth:2.0:oob` ). I framtiden kommer vi att behöva ändra denna standard, så vi rekommenderar att du använder `https://login.microsoftonline.com/common/oauth2/nativeclient` .

- Om du skapar en ursprunglig mål-C-eller Swift-app för macOS registrerar du omdirigerings-URI: n baserat på programmets paket-ID i följande format: msauth. <your.app.bundle.id>://auth. ersätter <your.app.bundle.id> med programmets paket-ID.
- Om din app endast använder integrerad Windows-autentisering eller ett användar namn och ett lösen ord, behöver du inte registrera en omdirigerings-URI för programmet. Dessa flöden gör en tur och retur till Microsoft Identity Platform v 2.0-slutpunkten. Programmet kommer inte att anropas igen på någon specifik URI.
- Om du vill särskilja enhets kod flödet, integrerad Windows-autentisering och ett användar namn och ett lösen ord från ett konfidentiellt klient program flöde som inte har omdirigerings-URI: er antingen (det flöde för klientautentisering som används i daemon-program), måste du uttrycka att ditt program är ett offentligt klient program. För att uppnå den här konfigurationen går du till avsnittet **autentisering** för ditt program. I underavsnittet **Avancerade inställningar** i stycket **standard klient typ** väljer du **Ja** för att **behandla program som en offentlig klient**.

  ![Tillåt offentlig klient](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API-behörigheter

Skriv bords program anropar API: er för den inloggade användaren. De måste begära delegerade behörigheter. De kan inte begära program behörigheter, som endast hanteras i [daemon-program](scenario-daemon-overview.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Desktop-app: app-konfiguration](scenario-desktop-app-configuration.md)
