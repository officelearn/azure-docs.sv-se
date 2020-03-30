---
title: ta med fil
description: ta med fil
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 02/03/2020
ms.openlocfilehash: 5be6e7937a6e1f710b8e2576a9058963413fb6c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76984597"
---
1. I [Azure-portalen](https://ms.portal.azure.com/)väljer du **Azure Active Directory** > **App registreringar** > **Ny registrering**.

   [![Ny programregistrering i Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Din app visas här när du har registrerat den.

1. Ge programmet ett namn och välj **Konton i den här organisationskatalogen för** att ange de **kontotyper som stöds** som kan komma åt API:et. Välj en giltig URI att omdirigera användare till när de autentisera, sedan **Registrera**.

   [![Skapa programmet i Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Viktig Azure Active Directory-appinformation visas i appens **översiktsblad** i listan. Välj din app under **Ägda program**och sedan **Översikt**.

   [![Kopiera program-ID:et](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Kopiera ditt **program -ID (klient)** som ska användas i klientprogrammet.

1. **Autentiseringsbladet** anger viktiga konfigurationsinställningar för autentisering. 

    1. Lägg till **omdirigerings-URI:er** och konfigurera **accesstokens** genom att välja **+ Lägg till en plattform**.

    1. Ta reda på om appen är en **offentlig klient** eller inte genom att välja **Ja** eller **Nej**.

    1. Kontrollera vilka konton och klienter som stöds.

    [![Konfigurera implicit beviljande](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. När du har valt lämplig plattform konfigurerar du dina **omdirigerings-URI:er** och **åtkomsttoken** på sidopanelen till höger om användargränssnittet.

    1. **Omdirigera URI:er** måste matcha den adress som anges av autentiseringsbegäran:

        * För appar som finns i en lokal utvecklingsmiljö väljer du **Offentlig klient (mobil & skrivbord).** Se till att ange **den offentliga klienten** till **Ja**.
        * För ensidiga appar som finns på Azure App Service väljer du **Webb**.

    1. Ta reda på om en **utloggnings-URL** är lämplig.

    1. Aktivera det implicita beviljandeflödet genom att kontrollera **Åtkomsttoken** eller **ID-token**.

    [![Skapa omdirigerings-URI:er](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Klicka på **Konfigurera**och sedan **spara**.

1. Välj **Certifikat & hemligheter** sedan Ny klient **hemlighet** för att skapa ett program lösenord som din klientapp kan använda för att bevisa sin identitet.

   [![Skapa en ny klienthemlighet](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Ditt klienthemliga lösenord visas då. Kopiera nyckeln till din favorittextredigerare.

   > [!NOTE]
   > Du kan importera ett certifikat i stället. För ökad säkerhet rekommenderas ett certifikat. Om du vill använda ett certifikat väljer du **Ladda upp certifikat**.

1. Associera din Azure Active Directory-app Azure TIme Series Insights. Välj **API-behörigheter** > Lägg till en**behörighets-API:er****Add a permission** > som min organisation använder . 

    [![Associera ett API med din Azure Active Directory-app](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Skriv `Azure Time Series Insights` in i sökfältet och välj `Azure Time Series Insights`sedan .

1. Ange sedan vilken typ API-behörighet som appen kräver. Som standard markeras **delegerade behörigheter.** Välj en behörighetstyp och välj **lägg till behörigheter**.

    [![Ange vilken typ av API-behörighet appen kräver](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
