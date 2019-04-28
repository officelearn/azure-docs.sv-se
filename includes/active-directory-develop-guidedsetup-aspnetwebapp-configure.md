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
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2cdc6ea01e6c3555740102f319d0f4e8e4fc1c22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298232"
---
## <a name="register-your-application"></a>Registrera ditt program

För att registrera ditt program och lägga till din registreringsinformation för programmet i din lösning, har du två alternativ:

### <a name="option-1-express-mode"></a>Alternativ 1: Express-läge

Du kan snabbt registrera ditt program genom att göra följande:

1. Gå till den nya [Azure portal – appregistreringar](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) fönstret.
1. Ange ett namn för programmet och klicka på **Registrera**.
1. Följ anvisningarna för att ladda ned och konfigurera det nya programmet automatiskt med ett enda klick.

### <a name="option-2-advanced-mode"></a>Alternativ 2: Avancerat läge

Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:

1. Gå till Visual Studio och:
   1. Välj projektet i Solution Explorer och titta på fönstret Egenskaper (om du inte ser en egenskapsfönstret trycker på F4).
   1. Ändra SSL aktiverat till `True`.
   1. Högerklicka på projektet i Visual Studio och välj sedan **egenskaper**, och **Web** fliken. I den *servrar* avsnittet Ändra den *Url för Project* vara SSL-URL.
   1. Kopiera URL: en för SSL. Du lägger till denna URL i listan över omdirigerings-URL: er i portalen för registrering lista över omdirigerings-URL: er i nästa steg:<br/><br/>![Projektegenskaper](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Om ditt konto ger dig tillgång till fler än en klientorganisation väljer du ditt konto i det övre högra hörnet och ställer in din portalsession på önskad Azure AD-klientorganisation.
1. Gå till Microsoft identity-plattformen för utvecklare [appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) sidan.
1. Välj **ny registrering**.
1. När sidan **Registrera ett program** visas anger du programmets registreringsinformation:
   1. I avsnittet **Namn** anger du ett beskrivande programnamn som ska visas för appens användare, till exempel `ASPNET-Tutorial`.
   1. Lägg till SSL-URL som du har kopierat från Visual Studio i steg 1 (till exempel `https://localhost:44368/`) i **svars-URL**, och klicka på **registrera**.
1. Välj **autentiseringsmenyn** och ange **ID-token** under **Implicit beviljande** och välj sedan **Spara**.
1. Lägg till följande i `web.config` finns i rotmappen i avsnittet `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Ersätt `ClientId` med program-ID som du just registrerade.
1. Ersätt `redirectUri` med SSL-URL: en för ditt projekt.
