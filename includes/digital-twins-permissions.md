---
title: ta med fil
description: ta med fil
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
ms.custom: include file
ms.openlocfilehash: cfe3eb4c0ac1378b7c519b3b34094945612d8508
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029134"
---
>[!NOTE]
>Det här avsnittet innehåller anvisningar för [registrering av Azure AD-appar](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Öppna **Azure Active Directory** från den expanderade vänstra menyn i [Azure Portal](https://portal.azure.com)och öppna sedan fönstret **Appregistreringar** . 

    [![väljer Azure Active Directorys fönstret](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Välj knappen **+ ny registrering** .

    [![Välj knappen ny registrering](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Ge appregistreringen ett eget namn i rutan **Namn**. 

    1. Under avsnittet **omdirigerings-URI (valfritt)** anger du `https://microsoft.com` i text rutan.     

    1. Kontrol lera vilka konton och klienter som stöds av din Azure Active Directory-app.

    1. Välj **Registrera**.

    [![skapa fönster](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Bladet **autentisering** anger viktiga konfigurations inställningar för autentisering. 

    1. Lägg till **omdirigerings-URI: er** och konfigurera **åtkomsttoken** genom att välja **+ Lägg till en plattform**.

    1. Välj **Ja** om du vill ange att appen är en **offentlig klient**.

    1. Kontrol lera vilka konton och klienter som stöds av din Azure Active Directory-app.

    [konfigurations inställning för ![offentlig klient](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. När du har valt en lämplig plattform konfigurerar du **omdirigerings-URI: er** och **åtkomsttoken** på sido panelen till höger om användar gränssnittet.

    1. **Omdirigerings-URI: er** måste matcha adressen som anges av autentiseringsbegäran:

        * För appar som finns i en lokal utvecklings miljö väljer du **offentlig klient (mobil & Desktop)** . Se till att ange en **offentlig klient** till **Ja**.
        * För appar på en sida som finns på Azure App Service väljer du **webb**.

    1. Avgör om en **utloggnings-URL** är lämplig.

    1. Aktivera det implicita tilldelnings flödet genom att kontrol lera **åtkomsttoken** eller **ID-token**.
                
    [![konfigurera omdirigerings-URI: er](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    Klicka på **Konfigurera**och sedan på **Spara**.

1.  Öppna **översikts** fönstret för din registrerade app och Kopiera värdena för följande entiteter till en temporär fil. Du använder dessa värden för att konfigurera exempel programmet i följande avsnitt.

    - **Program-ID (klient)**
    - **Katalog (klient) ID**

    [![Azure Active Directory program-ID](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Öppna fönstret **API-behörigheter** för din app-registrering. Välj **+ Lägg till en behörighet** -knapp. I rutan **begär API-behörigheter** väljer du fliken **API: er min organisation använder** och söker sedan efter något av följande:
    
    1. `Azure Digital Twins`. Välj **Azure Digitals dubbla** API: er.

        [![Sök-API eller digitala Azure-dubbla](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Du kan också söka efter `Azure Smart Spaces Service`. Välj API för **Azure Smart Spaces-tjänsten** .

        [![Sök-API för Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Det API-namn och ID för Azure AD som visas beror på din klient organisation:
    > * Testa klient organisation och kund konton bör söka efter `Azure Digital Twins`.
    > * Andra Microsoft-konton bör söka efter `Azure Smart Spaces Service`.

1. Antingen visas API: et som **Azure Digitals dubbla** i samma **begär ande-API-behörigheter** när det är markerat. Välj List rutan **Läs** och markera kryss rutan **Läs. Skriv** . Välj knappen **Lägg till behörigheter** .

    [![Lägga till API-behörigheter](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Beroende på organisationens inställningar kan du behöva vidta ytterligare åtgärder för att bevilja administratörs åtkomst till det här API: et. Kontakta administratören om du vill ha mer information. När administratörs åtkomsten har godkänts visas din behörighet i kolumnen **Administratörs medgivande** i rutan **API-behörigheter** . 

    [![godkännande av administratörs medgivande](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Kontrol lera att **Azure Digital-dubbla** visas.
