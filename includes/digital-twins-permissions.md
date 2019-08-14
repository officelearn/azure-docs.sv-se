---
title: ta med fil
description: ta med fil
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: fc2e2fc05de66de6f428e6b8ca7c94f82003ba2a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012292"
---
>[!NOTE]
>Det här avsnittet innehåller anvisningar för den [nya Azure AD-appens registrering](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Om du fortfarande har en äldre intern app-registrering kan du använda den så länge den stöds. Om den nya metoden för registrering av appar inte fungerar i konfigurationen kan du dessutom försöka skapa en äldre AAD-app. Läs [Registrera din Azure Digital-appen med Azure Active Directory äldre](../articles/digital-twins/how-to-use-legacy-aad.md) för mer information. 

1. Öppna **Azure Active Directory** i den vänstra rutan i [Azure Portal](https://portal.azure.com)och öppna sedan fönstret **Appregistreringar** . Välj knappen **ny registrering** .

    ![Registrerad app](./media/digital-twins-permissions/aad-app-register.png)

1. Ge appregistreringen ett eget namn i rutan **Namn**. Under avsnittet omdirigerings- **URI (valfritt)** väljer du **offentlig klient (mobil & Desktop)** i list rutan till vänster och anger `https://microsoft.com` i text rutan till höger. Välj **Registrera**.

    ![Skapa fönster](./media/digital-twins-permissions/aad-app-reg-create.png)

1. För att se till att [appen är registrerad som en *inbyggd app*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)öppnar du fönstret **autentisering** för din app-registrering och bläddrar nedåt i fönstret. I avsnittet **standard klient typ** väljer du **Ja** för att **behandla program som en offentlig klient**. 

    ![Inbyggd standard](./media/digital-twins-permissions/aad-app-default-native.png)

1.  Öppna fönstret **Översikt** för din registrerade app och Kopiera värdena för följande entiteter till en temporär fil. Du använder dessa värden för att konfigurera exempel programmet i följande avsnitt.

    - **Program-ID (klient)**
    - **Katalog (klient) ID**

    ![ID för Azure Active Directory-programmet](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Öppna fönstret **API-behörigheter** för din app-registrering. Välj **Lägg till en behörighet** -knapp. I rutan **begär API-behörigheter** väljer du fliken **API: er min organisation använder** och söker sedan efter:
    
    1. **Azure Digital-dubbla**. Välj **Azure Digitals dubbla** API: er.

        ![Sök-API eller digitala Azure-dubbla](./media/digital-twins-permissions/aad-aap-search-api-dt.png)

    1. Du kan också söka efter **Azure Smart Spaces**. Välj API för **Azure Smart Spaces-tjänsten** .

        ![Sök-API för Azure Smart Spaces](./media/digital-twins-permissions/aad-app-search-api.png)

    > [!NOTE]
    > Det exakta namnet som visas när sökningen sker kan variera beroende på vilken Azure-klient som du tillhör.

1. Det valda API: et visas som **Azure Digitals dubbla** i samma **API-behörigheter för begäran** . Välj List rutan **Läs (1)** och välj sedan **Läs. Skriv** -kryss rutan. Välj knappen **Lägg till behörigheter** .

    ![Lägg till API-behörigheter](./media/digital-twins-permissions/aad-app-req-permissions.png)

1. Beroende på organisationens inställningar kan du behöva vidta ytterligare åtgärder för att bevilja administratörs åtkomst till det här API: et. Kontakta administratören om du vill ha mer information. När administratörs åtkomsten har godkänts visas kolumnen **Administratörs medgivande som krävs** i rutan API- **behörigheter** som liknar följande för dina API: er:

    ![Lägg till API-behörigheter](./media/digital-twins-permissions/aad-app-admin-consent.png)

