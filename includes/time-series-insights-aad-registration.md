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
ms.date: 10/02/2020
ms.openlocfilehash: 50f203357d29d450f5b34593952f611f025b14ff
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95560878"
---
1. I [Azure Portal](https://ms.portal.azure.com/)väljer du **Azure Active Directory**  >  **Appregistreringar**  >  **ny registrering**.

   [![Ny program registrering i Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Din app visas här när du har registrerat den.

1. Ge programmet ett namn och välj **konton i den här organisations katalogen endast** för att ange de **konto typer som stöds** och som kan komma åt API: et. Om du skapar en [offentlig klient-app](../articles/active-directory/develop/msal-client-application-configuration.md#redirect-uri)lägger du till en giltig omdirigerings-URI och **registrerar** sedan.

   [![Skapa programmet i Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Viktig information om Azure Active Directory-appen visas i bladet **Översikt** för appen. Välj din app under **ägda program** och sedan på **Översikt**.

   [![Kopiera program-ID](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Kopiera ditt **program (klient)-ID** som ska användas i klient programmet.

1. Bladet **autentisering** anger viktiga konfigurations inställningar för autentisering.

    1. Lägg till **omdirigerings-URI: er** och konfigurera **åtkomsttoken** genom att välja **+ Lägg till en plattform**.

    1. Ta reda på om appen är en **offentlig klient** eller inte genom att välja **Ja** eller **Nej**.

    1. Kontrol lera vilka konton och innehavare som stöds.

    [![Konfigurera implicit beviljande](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. När du har valt en lämplig plattform konfigurerar du **omdirigerings-URI: er** och **åtkomsttoken** på sido panelen till höger om användar gränssnittet.

    1. **Omdirigerings-URI: er** måste matcha adressen som anges av autentiseringsbegäran:

        * För appar som finns i en lokal utvecklings miljö väljer du **offentlig klient (mobil & Desktop)**. Se till att ange en **offentlig klient** till **Ja**.
        * För Single-Page appar som finns på Azure App Service väljer du **webb**.

    1. Avgör om en **utloggnings-URL** är lämplig.

    1. Aktivera det implicita tilldelnings flödet genom att kontrol lera **åtkomsttoken** eller **ID-token**.

    [![Skapa omdirigerings-URI: er](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Klicka på **Konfigurera** och sedan på **Spara**.

1. Välj **certifikat & hemligheter** och sedan **ny klient hemlighet** för att skapa ett program lösen ord som din klient program kan använda för att bevisa sin identitet.

   [![Skapa en ny klient hemlighet](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Ditt klient hemliga lösen ord visas sedan. Kopiera nyckeln till din favorit text redigerare.

   > [!NOTE]
   > Du kan importera ett certifikat i stället. Ett certifikat rekommenderas för förbättrad säkerhet. Om du vill använda ett certifikat väljer du **Ladda upp certifikat**.

1. Associera Azure Active Directory app-Azure Time Series Insights. Välj **API-behörigheter**  >  **Lägg till en behörighets**-  >  **API min organisation använder**.

    [![Associera ett API med din Azure Active Directory-app](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Skriv `Azure Time Series Insights` i Sök fältet och välj sedan `Azure Time Series Insights` .

1. Ange sedan den typ-API-behörighet som din app kräver. Som standard är **delegerade behörigheter** markerade. Välj en behörighets typ och välj sedan **Lägg till behörigheter**.

    [![Ange vilken typ av API-behörighet som appen kräver](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)