---
title: Hantera autentisering | Microsoft Azure Maps
description: Använd Azure Portal för att hantera autentisering i Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335533"
---
# <a name="manage-authentication-in-azure-maps"></a>Hantera autentisering i Azure Maps

När du har skapat ett Azure Maps-konto skapas ett klient-ID och nycklar som stöder Azure Active Directory (Azure AD)-autentisering och autentisering med delad nyckel.

## <a name="view-authentication-details"></a>Visa information om autentisering

När du har skapat ett Azure Maps-konto genereras de primära och sekundära nycklarna. Vi rekommenderar att du använder en primär nyckel som en prenumerations nyckel när du [använder autentisering med delad nyckel för att anropa Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Du kan använda en sekundär nyckel i scenarier som löpande nyckel ändringar. Mer information finns i [autentisering i Azure Maps](https://aka.ms/amauth).

Du kan visa information om autentiseringen i Azure Portal. I ditt konto går du till menyn **Inställningar** och väljer **autentisering**.

![Information om autentisering](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Registrera och konfigurera en Azure AD-App

1. I listan med Azure-tjänster i Azure Portal väljer du **Azure Active Directory** > **Appregistreringar** > **ny registrering**.  

    ![Appregistrering](./media/how-to-manage-authentication/app-registration.png)

1. Om du redan har registrerat appen fortsätter du till nästa steg. Om du inte har registrerat appen, ange ett **namn**, Välj en **Support konto typ**och välj sedan **Registrera**.  

    ![Registrerings information för appen](./media/how-to-manage-authentication/app-create.png)

1. Om du vill tilldela delegerade API-behörigheter till Azure Maps går du till programmet. Under **Appregistreringar**väljer du **API-behörigheter** > **Lägg till en behörighet**. Sök efter och välj **Azure Maps**under **API: er som används i organisationen**.

    ![Lägg till API-behörigheter för app](./media/how-to-manage-authentication/app-permissions.png)

1. Markera kryss rutan bredvid **åtkomst Azure Maps**och välj sedan **Lägg till behörigheter**.

    ![Välj API-behörigheter för app](./media/how-to-manage-authentication/select-app-permissions.png)

1. Utför ett av följande steg, beroende på din autentiseringsmetod. 

    * Om ditt program använder user-token-autentisering med Azure Maps Web SDK, aktiverar `oauth2AllowImplicitFlow`du. Om du vill aktivera det anger **Manifest** `oauth2AllowImplicitFlow` du true i avsnittet manifest i appens registrering. 
    
       ![App-manifest](./media/how-to-manage-authentication/app-manifest.png)

    * Om programmet använder Server-eller programautentisering kan du gå till **certifikat & hemligheter**på din app Registration-sida. Överför sedan antingen ett offentligt nyckel certifikat eller skapa ett lösen ord genom att välja **ny klient hemlighet**. 
    
       ![Skapa en klient hemlighet](./media/how-to-manage-authentication/app-keys.png)

        Om du skapar ett lösen ord kan du när du har valt **Lägg till**, kopiera lösen ordet och lagra det på ett säkert sätt. Du använder det här lösen ordet för att hämta tokens från Azure AD.

       ![Lägg till en klient hemlighet](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Bevilja rollbaserad åtkomst kontroll till Azure Maps

När du har associerat ett Azure Maps-konto med din Azure AD-klient kan du bevilja åtkomst kontroll. Du beviljar *rollbaserad åtkomst kontroll* (RBAC) genom att tilldela en användare, grupp eller ett program till en eller flera Azure Maps åtkomst kontroll roller. 

1. Gå till ditt **Azure Maps-konto**. Välj**roll tilldelningen** **åtkomst kontroll (IAM)** > .

    ![Bevilja RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. På fliken **roll tilldelningar** under **roll**väljer du **Azure Maps datum läsare (för hands version)**. Under **tilldela åtkomst till**väljer du **Azure AD-användare, grupp eller tjänstens huvud namn**. Välj användaren eller programmet. Välj sedan **Spara**.

    ![Lägg till rolltilldelning](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Visa tillgängliga Azure Maps RBAC-roller

Om du vill visa RBAC-roller som är tillgängliga för Azure Maps går du till **åtkomst kontroll (IAM)**. Välj **roller**och Sök sedan efter roller som börjar med *Azure Maps*. Dessa Azure Maps roller är de roller som du kan bevilja åtkomst till.

![Visa tillgängliga roller](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Visa Azure Maps RBAC

RBAC ger detaljerad åtkomst kontroll.

Om du vill visa användare och appar som har beviljats RBAC för Azure Maps går du till **Access Control (IAM)**. Där väljer du **roll tilldelningar**och filtrerar sedan efter **Azure Maps**.

![Visa användare och appar som har beviljats RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Begär token för Azure Maps

När du har registrerat din app och associerar den med Azure Maps kan du begära åtkomst-token.

Om ditt program använder autentisering med användar-token med Azure Maps Web SDK konfigurerar du din HTML-sida med Azure Maps-klient-ID och ID för Azure AD-App.

Om programmet använder Server-eller programautentisering ska du begära en token från Azure AD-token-slutpunkten `https://login.microsoftonline.com`. Använd följande information i din begäran: 

* Resurs-ID för Azure AD`https://atlas.microsoft.com/`
* Azure Maps klient-ID
* ID för Azure AD-App
* Lösen ord eller certifikat för Azure AD App-registrering

| Azure-miljö   | Azure AD-token-slutpunkt | Resurs-ID för Azure |
| --------------------|-------------------------|-------------------|
| Offentligt Azure-moln        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government molnet   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Mer information om hur du begär åtkomsttoken från Azure AD för användare och tjänst huvud namn finns i [autentiserings scenarier för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Nästa steg

Mer information finns i [Azure AD och Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Hitta API-användnings mått för ditt Azure Maps-konto:
> [!div class="nextstepaction"] 
> [Visa användningsstatistik](how-to-view-api-usage.md)

Utforska exempel som visar hur du integrerar Azure AD med Azure Maps:

> [!div class="nextstepaction"]
> [Exempel på Azure AD-autentisering](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
