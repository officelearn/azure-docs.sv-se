---
title: ta med fil
description: ta med fil
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 06/28/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 324f41055cf333081f308a3ff533ff7df6b33038
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479253"
---
>[!NOTE]
>Det här avsnittet innehåller anvisningar om hur den [nya Azure AD-appregistrering](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Om du har fortfarande äldre inbyggd app-registrering, kan du använda den som stöds. Om det nya sättet att appen registation av någon anledning inte fungerar i din konfiguration, kan du dessutom försöker skapa en äldre inbyggd AAD-app. Läs [registrera din Azure Digital Twins-app med Azure Active Directory äldre](../articles/digital-twins/how-to-use-legacy-aad.md) för mer information. 

1. I den [Azure-portalen](https://portal.azure.com)öppnar **Azure Active Directory** från den vänstra rutan och sedan öppna den **appregistreringar** fönstret. Välj den **ny registrering** knappen.

    ![Registrerad App](./media/digital-twins-permissions/aad-app-register.png)

1. Ge appregistreringen ett eget namn i rutan **Namn**. Under den **omdirigerings-URI (valfritt)** väljer **offentlig klient (mobilappar och skrivbordsappar)** i listan till vänster och ange `https://microsoft.com` i textrutan till höger. Välj **Registrera**.

    ![Skapa fönster](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Se till att [appen registreras som en *inbyggd app*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration)öppnar den **autentisering** fönstret för appregistrering och rulla nedåt i det här fönstret. I den **standard klienttyp** väljer **Ja** för **hantera program som en offentlig klient**. 

    ![Standard interna](./media/digital-twins-permissions/aad-app-default-native.png)

1.  Öppna den **översikt** fönstret av appens registrerade och kopiera värdena för följande enheter till en temporär fil. Så här konfigurerar du exempelprogrammet i följande avsnitt ska du använda dessa värden.

    - **ID för programmet (klient)**
    - **Katalog (klient)-ID**

    ![ID för Azure Active Directory-programmet](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Öppna den **API-behörigheter** fönstret för appregistrering av din. Välj **lägga till en behörighet** knappen. I den **begär API-behörigheter** väljer den **API: er som min organisation använder** fliken och sök sedan efter **Azure Smart blanksteg**. Välj den **Azure Smart blanksteg Service** API.

    ![Sök-API](./media/digital-twins-permissions/aad-app-search-api.png)

1. Det valda API: T visas som **Azure Digital Twins** i samma **begär API-behörigheter** fönstret. Välj den **Läs (1)** listrutan och välj sedan **Read.Write** kryssrutan. Välj den **Lägg till behörigheter** knappen.

    ![Lägg till API-behörigheter](./media/digital-twins-permissions/aad-app-req-permissions.png)

1. Beroende på organisationens inställningar kan behöva du vidta ytterligare åtgärder för att ge administratörsåtkomst till detta API. Kontakta administratören för mer information. När administratörsåtkomsten har godkänts i **ADMINISTRATÖREN godkänna krävs** kolumnen i den **API-behörigheter** fönstret visas liknar följande för dina API: er:

    ![Lägg till API-behörigheter](./media/digital-twins-permissions/aad-app-admin-consent.png)

