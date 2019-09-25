---
title: 'Skriv bords app som anropar webb-API: er (app-registrering) – Microsoft Identity Platform'
description: 'Lär dig hur du skapar en stationär app som anropar webb-API: er (app-registrering)'
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 648652ed32a5dea30de665b7fa49190171a7f10a
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268394"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Stationär app som anropar webb-API: er – app-registrering

Den här artikeln innehåller appens registrerings information för ett Skriv bords program.

## <a name="supported-accounts-types"></a>Typer av konton som stöds

De konto typer som stöds i Skriv bords program beror på vilken upplevelse du vill se. På grund av den här relationen är de konto typer som stöds beroende av de flöden som du vill använda.

### <a name="audience-for-interactive-token-acquisition"></a>Mål grupp för hämtning av interaktiva token

Om ditt Skriv bords program använder interaktiv autentisering kan du logga in användare från vilken [Kontotyp](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)som helst.

### <a name="audience-for-desktop-app-silent-flows"></a>Mål grupp för tyst flöden i Desktop-appen

- Om du vill använda integrerad Windows-autentisering eller användar namn/lösen ord måste ditt program logga in användare i din egen klient organisation (LOB-utvecklare) eller i Azure Active Directory-organisationer (ISV-scenario). Dessa autentiserings flöden stöds inte för Microsoft-personliga konton.
- Om du vill använda enhets kod flödet kan du inte logga in användare med sina Microsoft-personliga konton ännu.
- Om du loggar in användare med sociala identiteter som skickar en B2C-auktoritet och-princip kan du bara använda autentiseringen interaktiv och username-Password.

## <a name="redirect-uris"></a>Omdirigerings-URI:er

Omdirigerings-URI: erna som ska användas i Skriv bords program beror på det flöde som du vill använda.

- Om du använder den **interaktiva autentiseringen** eller **enhets kod flödet**ska du använda `https://login.microsoftonline.com/common/oauth2/nativeclient`. Du uppnår den här konfigurationen genom att klicka på motsvarande URL i avsnittet **autentisering** för ditt program.
  
  > [!IMPORTANT]
  > I dag MSAL.NET används en annan omdirigerings-URI som standard i Skriv`urn:ietf:wg:oauth:2.0:oob`bords program som körs på Windows (). I framtiden ska vi ändra den här standardinställningen och därför rekommenderar vi att du använder`https://login.microsoftonline.com/common/oauth2/nativeclient`

- Om du skapar en intern mål-C-eller Swift-app för macOS vill du registrera redirectUri baserat på programmets paket-ID i följande format: **msauth. <. app. bundle. id >://auth** (Ersätt < ditt. app. bundle. ID > med programmets paket identifierare)
- Om din app endast använder integrerad Windows-autentisering eller användar namn/lösen ord, behöver du inte registrera en omdirigerings-URI för programmet. De här flödena gör en tur och retur till Microsoft Identity Platform v 2.0-slutpunkten och programmet kommer inte att anropas tillbaka på någon specifik URI.
- För att skilja enhets kod flödet, integrerad Windows-autentisering och användar namn/lösen ord från ett konfidentiellt klient program flöde som inte har omdirigerings-URI: er, antingen (det flöde för klientautentisering som används i daemon-program), måste du uttrycka att ditt programmet är ett offentligt klient program. För att uppnå den här konfigurationen går du till avsnittet **autentisering** för ditt program. I underavsnittet **Avancerade inställningar** i stycket **standard klient typ** väljer du sedan **Ja** för frågan **behandla programmet som en offentlig klient**.

  ![Tillåt offentlig klient](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API-behörigheter

Skriv bords program anropar API: er för den inloggade användaren. De måste begära delegerade behörigheter. De kan dock inte begära program behörigheter, som endast hanteras i daemon- [program](scenario-daemon-overview.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [App-app-konfiguration](scenario-desktop-app-configuration.md)
