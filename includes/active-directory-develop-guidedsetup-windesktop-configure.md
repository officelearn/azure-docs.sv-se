---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: c4dc3d422e18d9ee41bf16ac3e6f22c3d7e466d7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199510"
---
## <a name="register-your-application"></a>Registrera ditt program

Du kan registrera ditt program på något av två sätt.

### <a name="option-1-express-mode"></a>Alternativ 1: Express-läge

Du kan snabbt registrera ditt program genom att göra följande:
1. Gå till den [Azure portal – programregistrering](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Ange ett namn för programmet och välj **Registrera**.
1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.

### <a name="option-2-advanced-mode"></a>Alternativ 2: Avancerat läge

Du registrerar programmet och lägger till programregistreringsinformationen i din lösning genom att göra följande:
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. Gå till Microsoft identity-plattformen för utvecklare [appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan.
1. Välj **ny registrering**.
   - I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `Win-App-calling-MsGraph`.
   - I avsnittet **Kontotyper som stöds** väljer du **Konton alla organisationskataloger och personliga Microsoft-konton (till exempel Skype, Xbox och Outlook.com)**.
   - Välj **Registrera** för att skapa programmet.
1. I listan över sidor för appen väljer du **Autentisering**.
   1. I den **omdirigerings-URI: er** avsnittet i listan över omdirigerings-URI: er:
   1. I den **typ** kolumn väljer **offentlig klient (mobila och stationära)**.
   1. Ange `urn:ietf:wg:oauth:2.0:oob` i den **OMDIRIGERINGS-URI** kolumn.
1. Välj **Spara**.
1. Gå till Visual Studio, öppna den *App.xaml.cs* filen och Ersätt sedan `Enter_the_Application_Id_here` med program-ID som du just registrerade och kopieras.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
