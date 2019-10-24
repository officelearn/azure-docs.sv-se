---
title: Hantera autentisering i Azure Maps | Microsoft Docs
description: Du kan använda Azure Portal för att hantera autentisering i Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 07/11/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2f4a3d791e6b5d6ff20c09408d1a0bf5995c32fd
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756563"
---
# <a name="manage-authentication-in-azure-maps"></a>Hantera autentisering i Azure Maps

När du har skapat ett Azure Maps-konto skapas ett klient-ID och nycklar som stöder antingen Azure Active Directory (Azure AD) eller autentisering med delad nyckel.

## <a name="view-authentication-details"></a>Visa information om autentisering

Du kan visa information om autentiseringen på Azure Portal. Gå till ditt konto och välj **autentisering** på menyn **Inställningar** .

![Information om autentisering](./media/how-to-manage-authentication/how-to-view-auth.png)

 Mer information finns i [autentisering med Azure Maps](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Konfigurera registrering av Azure AD-App

När du har skapat ett Azure Maps-konto måste du upprätta en länk mellan din Azure AD-klient och Azure Maps-resursen.

1. Gå till bladet Azure AD och skapa en app-registrering. Ange ett namn för registreringen. I rutan **inloggnings-URL** anger du start sidan för WEBBAPP/API (till exempel https: \//localhost/). Om du redan har en registrerad app går du till steg 2.

    ![Appregistrering](./media/how-to-manage-authentication/app-registration.png)

    ![Registrerings information för appen](./media/how-to-manage-authentication/app-create.png)

2. Om du vill tilldela delegerade API-behörigheter till Azure Maps går du till programmet under **Appregistreringar**och väljer sedan **Inställningar**.  Välj **nödvändiga behörigheter**och välj sedan **Lägg till**. Sök efter och välj **Azure Maps** under **Välj ett API**och välj sedan knappen **Välj** .

    ![API-behörigheter för app](./media/how-to-manage-authentication/app-permissions.png)

3. Välj **åtkomst Azure Maps**under **Välj behörigheter**och välj sedan knappen **Välj** .

    ![Välj API-behörigheter för app](./media/how-to-manage-authentication/select-app-permissions.png)

4. Slutför steget a eller b, beroende på din autentiseringsmetod.

    1. Om ditt program använder autentisering med användar-token med Azure Maps-webbsdk, aktiverar du `oauthEnableImplicitFlow` genom att ställa in det på sant i avsnittet manifest på informations sidan för appens registrering.
    
       ![App-manifest](./media/how-to-manage-authentication/app-manifest.png)

    2. Om programmet använder Server/program-autentisering går du till bladet **nycklar** i appens registrering och skapar antingen ett lösen ord eller laddar upp ett certifikat för offentlig nyckel till appens registrering. Om du skapar ett lösen ord kan du, när du har valt **Spara**, kopiera lösen ordet för senare och lagra det på ett säkert sätt. Du använder det här lösen ordet för att hämta token från Azure AD.

       ![App-nycklar](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Bevilja RBAC till Azure Maps

När du har kopplat ett Azure Maps-konto till din Azure AD-klient kan du bevilja åtkomst kontroll genom att tilldela en användare, grupp eller program till en eller flera Azure Maps åtkomst kontroll roller.

1. Gå till **åtkomst kontroll (IAM)** , Välj **roll tilldelningar**och välj sedan **Lägg till roll tilldelning**.

    ![Bevilja RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. I fönstret **Lägg till roll tilldelning** under **roll**väljer du **Azure Maps datum läsare (för hands version)** . Under **tilldela åtkomst till**väljer du **Azure AD-användare, grupp eller tjänstens huvud namn**. Under **Välj**väljer du användaren eller programmet. Välj **Spara**.

    ![Lägg till roll tilldelning](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Visa tillgängliga Azure Maps RBAC-roller

Om du vill visa RBAC-roller (rollbaserad åtkomst kontroll) som är tillgängliga för Azure Maps går du till **åtkomst kontroll (IAM)** , väljer **roller**och söker sedan efter roller som börjar med **Azure Maps**. Det här är de roller som du kan bevilja åtkomst till.

![Visa tillgängliga roller](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Visa Azure Maps RBAC

RBAC ger detaljerad åtkomst kontroll.

Om du vill visa användare och appar som har beviljats RBAC för Azure Maps går du till **Access Control (IAM)** , väljer **roll tilldelningar**och filtrerar efter **Azure Maps**.

![Visa användare och appar som beviljats RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Begär token för Azure Maps

När du har registrerat din app och associerat den med Azure Maps kan du begära åtkomst-token.

* Om ditt program använder autentisering med användar-token med Azure Maps Web SDK, måste du konfigurera din HTML-sida med Azure Maps-klient-ID och Azure AD App-ID.

* Om programmet använder Server/program-autentisering måste du begära en token från Azure AD-inloggningens slut punkt `https://login.microsoftonline.com` med Azure AD-resurs-ID: t `https://atlas.microsoft.com/`, Azure Maps-klient-ID, Azure AD-App-ID och Azure AD-appens registrerings lösen ord eller certifikatmallens.

Mer information om hur du begär åtkomsttoken från Azure AD för användare och tjänst huvud namn finns i [autentiserings scenarier för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD-autentisering och Azure Maps Web SDK finns i [Azure AD och Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Lär dig hur du ser API-användnings mått för ditt Azure Maps-konto:
> [!div class="nextstepaction"] 
> [Visa användnings statistik](how-to-view-api-usage.md)

En lista över exempel som visar hur du integrerar Azure Active Directory (AAD) med Azure Maps finns i:

> [!div class="nextstepaction"]
> [Exempel på AAD-autentisering](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)