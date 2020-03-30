---
title: Registrera skrivbordsappar som anropar webb-API:er – Microsoft identity platform | Azure
description: Lär dig hur du skapar en skrivbordsapp som anropar webb-API:er (appregistrering)
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
ms.openlocfilehash: c55fc9eb94a88dba1ab9fc915fe84bc2dd7d4d40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76702189"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Skrivbordsapp som anropar webb-API:er: Appregistrering

Den här artikeln beskriver appregistreringsdetunderen för ett skrivbordsprogram.

## <a name="supported-account-types"></a>Kontotyper som stöds

Vilka kontotyper som stöds i ett skrivbordsprogram beror på vilken upplevelse du vill lysa upp. På grund av den här relationen beror de kontotyper som stöds på vilka flöden du vill använda.

### <a name="audience-for-interactive-token-acquisition"></a>Målgrupp för interaktivt tokenförvärv

Om ditt skrivbordsprogram använder interaktiv autentisering kan du logga in användare från valfri [kontotyp](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-desktop-app-silent-flows"></a>Målgrupp för tysta flöden i skrivbordsappar

- Om du vill använda integrerad Windows-autentisering eller ett användarnamn och ett lösenord måste programmet logga in användare i din egen klientorganisation, till exempel om du är en LOB-utvecklare (line-of-business). Eller i Azure Active Directory-organisationer måste ditt program logga in användare i din egen klient om det är ett ISV-scenario. Dessa autentiseringsflöden stöds inte för Microsofts personliga konton.
- Om du vill använda enhetskodflödet kan du inte logga in användare med deras personliga Microsoft-konton ännu.
- Om du loggar in användare med sociala identiteter som skickar en B2C-myndighet och princip (Business-to-Commerce) kan du bara använda den interaktiva autentiseringen och autentiseringen av användarnamn och lösenord.

## <a name="redirect-uris"></a>Omdirigera URI:er

Vilka omdirigerings-URI:er som ska användas i ett skrivbordsprogram beror på vilket flöde du vill använda.

- Om du använder interaktiv autentisering `https://login.microsoftonline.com/common/oauth2/nativeclient`eller enhetskodflöde använder du . Om du vill uppnå den här konfigurationen väljer du motsvarande URL i avsnittet **Autentisering** för ditt program.
  
  > [!IMPORTANT]
  > Idag använder MSAL.NET en annan omdirigera URI som standard i`urn:ietf:wg:oauth:2.0:oob`skrivbordsprogram som körs på Windows ( ). I framtiden vill vi ändra standardinställningen, så vi `https://login.microsoftonline.com/common/oauth2/nativeclient`rekommenderar att du använder .

- Om du skapar en inbyggd Objective-C- eller Swift-app för macOS registrerar du omdirigerings-URI:n baserat på programmets paketidentifierare i följande format: msauth.<your.app.bundle.id>://auth. Ersätt <your.app.bundle.id> med programmets paketidentifierare.
- Om appen bara använder integrerad Windows-autentisering eller ett användarnamn och ett lösenord behöver du inte registrera en omdirigera URI för ditt program. Dessa flöden gör en rundresa till Microsoft identity platform v2.0-slutpunkten. Ditt program anropas inte på någon specifik URI.
- Om du vill särskilja enhetskodflöde, integrerad Windows-autentisering och ett användarnamn och ett lösenord från ett konfidentiellt klientprogramflöde som inte heller har omdirigerat URI:er (klientautentiseringsflödet som används i demonprogram) måste du uttrycka det din ansökan är ett offentligt klientprogram. För att uppnå den här konfigurationen går du till avsnittet **Autentisering** för ditt program. I stycket **Avancerade inställningar** i stycket **Standardklienttyp** väljer du **Ja** för behandla program som en **offentlig klient**.

  ![Tillåt offentlig klient](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>API-behörigheter

Api:er för skrivbordsprogram anropar API:er för den inloggade användaren. De måste begära delegerade behörigheter. De kan inte begära programbehörigheter, som endast hanteras i [daemon-program](scenario-daemon-overview.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skrivbordsapp: Appkonfiguration](scenario-desktop-app-configuration.md)
