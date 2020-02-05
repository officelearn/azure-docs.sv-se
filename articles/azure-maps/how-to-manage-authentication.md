---
title: Hantera autentisering | Microsoft Azure Maps
description: Du kan använda Azure Portal för att hantera autentisering i Microsoft Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: f856aebe5e3acaca142e460d18ec8c6498b18787
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989312"
---
# <a name="manage-authentication-in-azure-maps"></a>Hantera autentisering i Azure Maps

När du har skapat ett Azure Maps-konto skapas ett klient-ID och nycklar som stöder Azure Active Directory (Azure AD) och autentisering med delad nyckel.

## <a name="view-authentication-details"></a>Visa information om autentisering

När du har skapat Azure Maps-kontot skapas de primära och sekundära nycklarna. Vi rekommenderar att du använder primär nyckel som prenumerations nyckel när du anropar Azure Maps med hjälp av [autentisering med delad nyckel](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Sekundär nyckel kan användas i scenarier som löpande nyckel ändringar. Mer information finns i [autentisering med Azure Maps](https://aka.ms/amauth).

Du kan visa information om autentiseringen på Azure Portal. Gå till ditt konto och välj **autentisering** på menyn **Inställningar** .

![Information om autentisering](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="configure-azure-ad-app-registration"></a>Konfigurera Azure AD App registrering

1. Välj **Azure Active Directory** i listan över Azure-tjänster i Azure Portal.  Välj **Appregistreringar** och klicka på **ny registrering**.  Nästföljande. Ange **namn**, Välj **Support konto typ**och klicka på **Registrera**.  Om du redan har en registrerad app fortsätter du till steg 2. 

    ![Appregistrering](./media/how-to-manage-authentication/app-registration.png)

    ![Registrerings information för appen](./media/how-to-manage-authentication/app-create.png)

2. Om du vill tilldela delegerade API-behörigheter till Azure Maps går du till programmet under **Appregistreringar**. Välj sedan **API-behörigheter**och välj sedan **Lägg till en behörighet**. Sök efter och välj **Azure Maps** under **API: er som används i organisationen**.

    ![API-behörigheter för app](./media/how-to-manage-authentication/app-permissions.png)

3. Kontrol lera **åtkomst Azure Maps** och klicka sedan på **Lägg till behörigheter**.

    ![Välj API-behörigheter för app](./media/how-to-manage-authentication/select-app-permissions.png)

4. Slutför steget a eller b, beroende på din autentiseringsmetod. 

    1. Aktivera `oauth2AllowImplicitFlow`om ditt program använder autentisering med användar-token med Azure Maps-webbsdk. Om du vill aktivera `oauth2AllowImplicitFlow`anger du det som sant i manifest avsnittet i appens registrering. 
    
       ![App-manifest](./media/how-to-manage-authentication/app-manifest.png)

    2. Om programmet använder Server/program-autentisering går du till bladet **certifikat & hemligheter** på appens registrerings sida och skapar antingen ett lösen ord genom att klicka på **ny klient hemlighet** eller ladda upp ett certifikat för offentlig nyckel till appens registrering. Om du skapar ett lösen ord kan du efter att ha klickat på **Lägg till**, kopiera lösen ordet för senare och lagra det på ett säkert sätt. Du använder det här lösen ordet för att hämta token från Azure AD.

       ![App-nycklar](./media/how-to-manage-authentication/app-keys.png)

       ![Lägg till nyckel](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-rbac-to-azure-maps"></a>Bevilja rollbaserad åtkomst kontroll (RBAC) till Azure Maps

När du har associerat ett Azure Maps-konto med din Azure AD-klient kan du bevilja åtkomst kontroll. Du beviljar åtkomst kontroll genom att tilldela en användare, grupp eller ett program till en eller flera Azure Maps åtkomst kontroll roller.

1. Gå till ditt **Azure Maps-konto**. Välj **åtkomst kontroll (IAM)** och välj sedan **roll tilldelning**.

    ![Bevilja RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. I fönstret **roll tilldelning** väljer du **Azure Maps datum läsare (för hands version)** under **roll**. Under **tilldela åtkomst till väljer du** **Azure AD-användare, grupp eller tjänst princip**. Välj användaren eller programmet. Välj **Spara**.

    ![Lägg till roll tilldelning](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Visa tillgängliga Azure Maps RBAC-roller

Om du vill visa RBAC-roller (rollbaserad åtkomst kontroll) som är tillgängliga för Azure Maps går du till **åtkomst kontroll (IAM)** , väljer **roller**och söker sedan efter roller som börjar med **Azure Maps**. Dessa roller är roller som du kan bevilja åtkomst till.

![Visa tillgängliga roller](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Visa Azure Maps RBAC

RBAC ger detaljerad åtkomst kontroll.

Om du vill visa användare och appar som har beviljats RBAC för Azure Maps går du till **Access Control (IAM)** , väljer **roll tilldelningar**och filtrerar efter **Azure Maps**.

![Visa användare och appar som beviljats RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Begär token för Azure Maps

När du har registrerat din app och associerat den med Azure Maps kan du begära åtkomst-token.

* Om ditt program använder autentisering med användar-token med Azure Maps Web SDK, måste du konfigurera din HTML-sida med Azure Maps-klient-ID och Azure AD App-ID.

* Om programmet använder Server/program-autentisering måste du begära en token från Azure AD token-slutpunkten `https://login.microsoftonline.com` med Azure AD-resurs-ID: t `https://atlas.microsoft.com/`, Azure Maps klient-ID, Azure AD-App-ID och Azure AD-appens registrerings lösen ord eller certifikat.

| Azure Environment   | Azure AD-token-slutpunkt | Resurs-ID för Azure |
| --------------------|-------------------------|-------------------|
| Azure Public        | https://login.microsoftonline.com | https://atlas.microsoft.com/ |
| Azure Government    | https://login.microsoftonline.us  | https://atlas.microsoft.com/ | 

Mer information om hur du begär åtkomsttoken från Azure AD finns i [autentiserings scenarier för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)för användare och tjänst huvud namn.


## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD-autentisering och Azure Maps Web SDK finns i [Azure AD och Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Lär dig hur du ser API-användnings mått för ditt Azure Maps-konto:
> [!div class="nextstepaction"] 
> [Visa användnings statistik](how-to-view-api-usage.md)

En lista över exempel som visar hur du integrerar Azure Active Directory (AAD) med Azure Maps finns i:

> [!div class="nextstepaction"]
> [Exempel på Azure AD-autentisering](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
