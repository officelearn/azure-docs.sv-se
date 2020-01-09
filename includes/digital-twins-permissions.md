---
title: ta med fil
description: ta med fil
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/06/2020
ms.custom: include file
ms.openlocfilehash: 6f8eaa1d13e7a8c4ea69118cdea4286f2dd90860
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692547"
---
>[!NOTE]
>Det här avsnittet innehåller anvisningar för [registrering av Azure AD-appar](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Öppna **Azure Active Directory** från den expanderade vänstra menyn i [Azure Portal](https://portal.azure.com)och öppna sedan fönstret **Appregistreringar** . 

    [![väljer Azure Active Directorys fönstret](./media/digital-twins-permissions/select-aad-pane.png)](./media/digital-twins-permissions/select-aad-pane.png#lightbox)

1. Välj knappen **+ ny registrering** .

    [![Välj knappen ny registrering](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Ge appregistreringen ett eget namn i rutan **Namn**. Under avsnittet **omdirigerings-URI (valfritt)** väljer du **offentlig klient/ursprunglig (mobil & Desktop)** i den nedrullningsbara menyn till vänster och anger `https://microsoft.com` i text rutan till höger. Välj **Registrera**.

    [![skapa fönster](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. För att se till att [appen är registrerad som en **offentlig klient**](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)öppnar du fönstret **autentisering** för din app-registrering och bläddrar nedåt i fönstret. I avsnittet **standard klient typ** väljer du **Ja** för att **behandla program som en offentlig klient**och trycker på **Spara**.

    Kontrol **lera åtkomsttoken för** att aktivera **oauth2AllowImplicitFlow** -inställningen i manifestet. JSON.

    [konfigurations inställning för ![offentlig klient](./media/digital-twins-permissions/aad-public-client.png)](./media/digital-twins-permissions/aad-public-client.png#lightbox)

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
