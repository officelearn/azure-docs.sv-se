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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77029134"
---
>[!NOTE]
>Det här avsnittet innehåller instruktioner för registrering av [Azure AD-appar](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Öppna Azure Active **Directory** från den expanderbara vänstra menyn i [Azure-portalen](https://portal.azure.com)och öppna sedan fönstret **Appregistreringar.** 

    [![Välj fönstret Azure Active Directory](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Välj knappen **+ Ny registrering.**

    [![Välj knappen Ny registrering](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Ge appregistreringen ett eget namn i rutan **Namn**. 

    1. Ange `https://microsoft.com` textrutan under **Avsnittet Omdirigera URI (valfritt).**     

    1. Verifiera vilka konton och klienter som stöds av din Azure Active Directory-app.

    1. Välj **Registrera**.

    [![Skapa fönster](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. **Autentiseringsbladet** anger viktiga konfigurationsinställningar för autentisering. 

    1. Lägg till **omdirigerings-URI:er** och konfigurera **accesstokens** genom att välja **+ Lägg till en plattform**.

    1. Välj **Ja** om du vill ange att appen är en **offentlig klient**.

    1. Verifiera vilka konton och klienter som stöds av din Azure Active Directory-app.

    [![Konfigurationsinställning för offentliga klienter](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. När du har valt lämplig plattform konfigurerar du dina **omdirigerings-URI:er** och **åtkomsttoken** på sidopanelen till höger om användargränssnittet.

    1. **Omdirigera URI:er** måste matcha den adress som anges av autentiseringsbegäran:

        * För appar som finns i en lokal utvecklingsmiljö väljer du **Offentlig klient (mobil & skrivbord).** Se till att ange **den offentliga klienten** till **Ja**.
        * För ensidiga appar som finns på Azure App Service väljer du **Webb**.

    1. Ta reda på om en **utloggnings-URL** är lämplig.

    1. Aktivera det implicita beviljandeflödet genom att kontrollera **Åtkomsttoken** eller **ID-token**.
                
    [![Konfigurera url:er för omdirigering](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    Klicka på **Konfigurera**och sedan **spara**.

1.  Öppna **fönstret Översikt** för din registrerade app och kopiera värdena för följande entiteter till en temporär fil. Du ska använda dessa värden för att konfigurera exempelprogrammet i följande avsnitt.

    - **Program-ID (klient)**
    - **Katalog-ID (klient)**

    [![ID för Azure Active Directory-programmet](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Öppna **api-behörighetsfönstret** för din appregistrering. Välj **+ Lägg till en behörighetsknapp.** I fönstret **Behörigheter för begäran om API** markerar du de **API:er som min organisation använder** och söker sedan efter något av följande:
    
    1. `Azure Digital Twins`. Välj **Azure Digital Twins** API.

        [![Sök-API eller Azure Digital Twins](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Alternativt kan du `Azure Smart Spaces Service`söka efter . Välj **Azure Smart Spaces Service** API.

        [![Sök API för Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Azure AD API-namn och ID som visas beror på din klient:
    > * Testa klient- och kundkonton bör söka `Azure Digital Twins`efter .
    > * Andra Microsoft-konton `Azure Smart Spaces Service`bör söka efter .

1. Båda API:erna visas som **Azure Digital Twins** i samma **api-behörighetsfönster för begäran** när de har valts. Markera listrutan **Läs** och markera kryssrutan **Läs.Skriv.** Välj knappen **Lägg till behörigheter.**

    [![Lägga till API-behörigheter](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Beroende på organisationens inställningar kan du behöva vidta ytterligare åtgärder för att ge administratörsåtkomst till det här API:et. Kontakta administratören för mer information. När administratörsåtkomsten har godkänts visar kolumnen **Administratörsmedgivande obligatoriskt** dina **behörigheter.** 

    [![Godkännande av administratörsgodkännande](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Kontrollera att **Azure Digital Twins** visas.
