---
title: ta med fil
description: ta med fil
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 07/02/2019
ms.openlocfilehash: a463e3cf475909c34054717460dc10dbba4ad8f0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544004"
---
> [!IMPORTANT]
> * Den nya **Azure Active Directory** > **appregistreringar** bladet ersätter äldre **Azure Active Directory** > **App registreringar (äldre)** bladet maj 2019.
> * App-registreringar skapas eller visas på bladet äldre visas automatiskt på det nya bladet.
> * Omfattande information om hur du migrerar till den nya miljön i Azure App registrering finns i [Azure App-registreringar utbildning guide](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide) och [Azure Active Directory-Quickstart](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. I den [Azure-portalen](https://ms.portal.azure.com/)väljer **Azure Active Directory** > **appregistreringar** > **ny registrering**.

   [![Ny programregistrering i Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > Panelen för registrering av nya Azure Active Directory App kan du filtrera de visade apparna genom att välja **ägs program**.

    Appen visas här när du har registrerat.

1. Ge programmet ett namn och välj **konton i den här organisationens katalogen** att ange den **stöds kontotyper** som kan komma åt API: et. Välj en giltig URI för att omdirigera användare till när de autentiserar sedan **registrera**.

   [![Skapa programmet i Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Viktig information för Azure Active Directory-appen visas i listan appens **översikt** bladet. Välj din app under **ägs program**, sedan **översikt**.

   [![Kopiera program-ID](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Kopiera din **(klient)-ID: T** du använder i klientprogrammet.

1. Den **autentisering** bladet anger konfigurationsinställningar för viktiga autentisering. 

    1. **Omdirigerings-URI: er** måste matcha den adress som tillhandahålls av autentiseringsbegäran:

        * Appar i en lokal utvecklingsmiljö, Välj **offentlig klient (mobila och stationära)** . Se till att ange den **standard klienttyp** på Ja.
        * Enkelsidiga appar i Azure App Service, Välj **Web**.

    1. Aktivera implicit beviljande av flödet genom att kontrollera **ID-token**.

   [![Skapa en ny klienthemlighet](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Klicka på **Spara**.

1. Koppla din Azure Active Directory app Azure TIme Series Insights. Välj **API-behörigheter** > **lägga till en behörighet** > **API: er som min organisation använder**. 

    [![Koppla ett API till din Azure Active Directory-app](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Typ `Azure Time Series Insights` till search liggande Välj `Azure Time Series Insights`.

1. Därefter ange vilken typ av API-behörighet som din app kräver. Som standard **delegerade behörigheter** markeras. Välj en behörighetstyp sedan och markera **Lägg till behörigheter**.

    [![Ange vilken typ av API-behörighet som din app kräver](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)