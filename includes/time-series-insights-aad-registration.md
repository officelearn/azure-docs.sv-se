---
title: ta med fil
description: ta med fil
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 08/08/2019
ms.openlocfilehash: ed5cd9424630a90b989b8a4d76959961f82a53ec
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935232"
---
> [!IMPORTANT]
> * Bladet ny **Azure Active Directory** > **Appregistreringar** ersätter bladet äldre **Azure Active Directory** > **Appregistreringar (bakåtkompatibelt)** 2019.
> * Appregistreringar som skapas eller visas på bladet äldre visas automatiskt på det nya bladet.
> * Omfattande information om hur du migrerar till den nya Azure App registrerings upplevelsen finns i [utbildnings guiden för Azure App-registrering](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide) och [Azure Active Directory snabb start](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. I [Azure Portal](https://ms.portal.azure.com/)väljer du **Azure Active Directory** > **Appregistreringar** > **ny registrering**.

   [![Ny program registrering i Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > På den nya registrerings panelen för Azure Active Directory appen kan du filtrera de visade apparna genom att välja **ägda program**.

    Din app visas här när du har registrerat den.

1. Ge programmet ett namn och välj **konton i den här organisations katalogen endast** för att ange de **konto typer som stöds** och som kan komma åt API: et. Välj en giltig URI för att omdirigera användare till efter att de autentiserats och **Registrera dig**sedan.

   [![Skapa programmet i Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Viktig information om Azure Active Directory-appen visas i bladet **Översikt** för appen. Välj din app under **ägda program**och sedan på **Översikt**.

   [![Kopiera program-ID](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Kopiera ditt **program (klient)-ID** som ska användas i klient programmet.

1. Bladet **autentisering** anger viktiga konfigurations inställningar för autentisering. 

    1. Omdirigerings- **URI: er** måste matcha adressen som anges av autentiseringsbegäran:

        * För appar som finns i en lokal utvecklings miljö väljer du **offentlig klient (mobil & Desktop)** . Se till att ange **standard klient typ** till Ja.
        * För appar på en sida som finns på Azure App Service väljer du **webb**.

    1. Aktivera det implicita tilldelnings flödet genom att kontrol lera **ID-token**.

   [![Skapa en ny klient hemlighet](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Klicka på **Spara**.

1. Välj **certifikat & hemligheter** och sedan **ny klient hemlighet** för att skapa ett program lösen ord som klienten kan använda för att bevisa sin identitet.

   [![Skapa en ny klient hemlighet](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Ditt klient hemliga lösen ord visas sedan. Kopiera nyckeln till din favorit text redigerare.

   > [!NOTE]
   > Du kan importera ett certifikat i stället. Ett certifikat rekommenderas för förbättrad säkerhet. Om du vill använda ett certifikat väljer du **Ladda upp certifikat**.

1. Associera din Azure Active Directory app Azure TIme Series Insights. Välj **API-behörigheter** > **Lägg till en behörighets** > -**API min organisation använder**. 

    [![Associera ett API med din Azure Active Directory-app](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Skriv `Azure Time Series Insights` i Sök fältet och välj `Azure Time Series Insights`sedan.

1. Ange sedan den typ-API-behörighet som din app kräver. Som standard är **delegerade behörigheter** markerade. Välj en behörighets typ och välj sedan **Lägg till behörigheter**.

    [![Ange vilken typ av API-behörighet som appen kräver](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)