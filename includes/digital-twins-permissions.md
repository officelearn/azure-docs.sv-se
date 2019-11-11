---
title: ta med fil
description: ta med fil
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 3ad4a3391a8d0174a0a031920f40a47464753767
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903947"
---
>[!NOTE]
>Det här avsnittet innehåller anvisningar för den [nya Azure AD-appens registrering](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Om du fortfarande har en äldre intern app-registrering kan du använda den så länge den stöds. Om den nya metoden för registrering av appar inte fungerar i konfigurationen kan du dessutom försöka skapa en äldre AAD-app. Läs [Registrera din Azure Digital-appen med Azure Active Directory äldre](../articles/digital-twins/how-to-use-legacy-aad.md) för mer information. 

1. Öppna **Azure Active Directory** från den expanderade vänstra menyn i [Azure Portal](https://portal.azure.com)och öppna sedan fönstret **Appregistreringar** . Välj knappen **ny registrering** .

    [![app registrerad](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Ge appregistreringen ett eget namn i rutan **Namn**. Under avsnittet **omdirigerings-URI (valfritt)** väljer du **offentlig klient/ursprunglig (mobil & Desktop)** i den nedrullningsbara menyn till vänster och anger `https://microsoft.com` i text rutan till höger. Välj **Registrera**.

    [![skapa fönster](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. För att se till att [appen är registrerad som en *inbyggd app*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)öppnar du fönstret **autentisering** för din app-registrering och bläddrar nedåt i fönstret. I avsnittet **standard klient typ** väljer du **Ja** för att **behandla program som en offentlig klient**och trycker på **Spara**.

    [![inbyggd standard](./media/digital-twins-permissions/aad-app-default-native.png)](./media/digital-twins-permissions/aad-app-default-native.png#lightbox)

1.  Öppna **översikts** fönstret för din registrerade app och Kopiera värdena för följande entiteter till en temporär fil. Du använder dessa värden för att konfigurera exempel programmet i följande avsnitt.

    - **Program-ID (klient)**
    - **Katalog (klient) ID**

    [![Azure Active Directory program-ID](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Öppna fönstret **API-behörigheter** för din app-registrering. Välj **Lägg till en behörighet** -knapp. I rutan **begär API-behörigheter** väljer du fliken **API: er min organisation använder** och söker sedan efter något av följande:
    
    1. `Azure Digital Twins`. Välj **Azure Digitals dubbla** API: er.

        [![Sök-API eller digitala Azure-dubbla](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Du kan också söka efter `Azure Smart Spaces Service`. Välj API för **Azure Smart Spaces-tjänsten** .

        [![Sök-API för Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Det API-namn och ID för Azure AD som visas beror på din klient organisation:
    > * Testa klient organisation och kund konton bör söka efter `Azure Digital Twins`.
    > * Andra Microsoft-konton bör söka efter `Azure Smart Spaces Service`.

1. Antingen är API: et när det är markerat visas som **Azure Digital-dubbla** i samma **begär ande-API-behörigheter** . Välj List rutan **Läs** och markera kryss rutan **Läs. Skriv** . Välj knappen **Lägg till behörigheter** .

    [![Lägga till API-behörigheter](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Beroende på organisationens inställningar kan du behöva vidta ytterligare åtgärder för att bevilja administratörs åtkomst till det här API: et. Kontakta administratören om du vill ha mer information. När administratörs åtkomsten har godkänts ser kolumnen **administrativt medgivande som krävs** i rutan **API-behörigheter** att se ut ungefär så här för dina API: er:

    [![Lägga till API-behörigheter](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

