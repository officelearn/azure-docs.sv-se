---
title: ta med fil
description: ta med fil
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 6e808fb64d004f117a3c25946ad97e26ddfadaa3
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802375"
---
>[!NOTE]
>Det här avsnittet innehåller anvisningar för den [nya Azure AD-appens registrering](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Om du fortfarande har en äldre intern app-registrering kan du använda den så länge den stöds. Om den nya metoden för registrering av appar inte fungerar i konfigurationen kan du dessutom försöka skapa en äldre AAD-app. Läs [Registrera din Azure Digital-appen med Azure Active Directory äldre](../articles/digital-twins/how-to-use-legacy-aad.md) för mer information. 

1. Öppna **Azure Active Directory** i den vänstra rutan i [Azure Portal](https://portal.azure.com)och öppna sedan fönstret **Appregistreringar** . Välj knappen **ny registrering** .

    [![Registrerad app](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Ge appregistreringen ett eget namn i rutan **Namn**. Under avsnittet **omdirigerings-URI (valfritt)** väljer du **offentlig klient (mobil & Desktop)** i list rutan till vänster och anger `https://microsoft.com` i text rutan till höger. Välj **Registrera**.

    [![Fönstret Skapa](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. För att se till att [appen är registrerad som en *inbyggd app*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)öppnar du fönstret **autentisering** för din app-registrering och bläddrar nedåt i fönstret. I avsnittet **standard klient typ** väljer du **Ja** för att **behandla program som en offentlig klient**. 

    [![Inbyggd standard](./media/digital-twins-permissions/aad-app-default-native.png)](./media/digital-twins-permissions/aad-app-default-native.png#lightbox)

1.  Öppna fönstret **Översikt** för din registrerade app och Kopiera värdena för följande entiteter till en temporär fil. Du använder dessa värden för att konfigurera exempel programmet i följande avsnitt.

    - **Program-ID (klient)**
    - **Katalog (klient) ID**

    [![Azure Active Directory program-ID](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Öppna fönstret **API-behörigheter** för din app-registrering. Välj **Lägg till en behörighet** -knapp. I rutan **begär API-behörigheter** väljer du fliken **API: er min organisation använder** och söker sedan efter:
    
    1. `Azure Digital Twins`. Välj **Azure Digitals dubbla** API: er.

        [![Sök-API eller digitala Azure-dubbla](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Du kan också söka efter `Azure Smart Spaces Service`. Välj API för **Azure Smart Spaces-tjänsten** .

        [![Sök-API för Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Det API-namn och ID för Azure AD som visas beror på din klient organisation:
    > * Testa klient organisation och kund konton bör söka efter `Azure Digital Twins`.
    > * Andra Microsoft-konton bör söka efter `Azure Smart Spaces Service`.

1. Det valda API: et visas som **Azure Digitals dubbla** i samma **API-behörigheter för begäran** . Välj List rutan **Läs (1)** och välj sedan **Läs. Skriv** -kryss rutan. Välj knappen **Lägg till behörigheter** .

    [![Lägga till API-behörigheter](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. Beroende på organisationens inställningar kan du behöva vidta ytterligare åtgärder för att bevilja administratörs åtkomst till det här API: et. Kontakta administratören om du vill ha mer information. När administratörs åtkomsten har godkänts visas kolumnen **Administratörs medgivande som krävs** i rutan **API-behörigheter** som liknar följande för dina API: er:

    [![Lägga till API-behörigheter](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

