---
title: Hantera autentisering | Microsoft Azure Maps
description: Använd Azure-portalen för att hantera autentisering i Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335533"
---
# <a name="manage-authentication-in-azure-maps"></a>Hantera autentisering i Azure Maps

När du har skapat ett Azure Maps-konto skapas ett klient-ID och nycklar för att stödja Azure Active Directory -autentisering (Azure AD) autentisering och autentisering med delad nyckel.

## <a name="view-authentication-details"></a>Visa autentiseringsinformation

När du har skapat ett Azure Maps-konto genereras de primära och sekundära nycklarna. Vi rekommenderar att du använder en primärnyckel som en prenumerationsnyckel när du [använder autentisering med delad nyckel för att anropa Azure Maps](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication). Du kan använda en sekundär nyckel i scenarier som rullande nyckeländringar. Mer information finns [i Autentisering i Azure Maps](https://aka.ms/amauth).

Du kan visa dina autentiseringsuppgifter i Azure-portalen. Där väljer du **Autentisering**på **menyn Inställningar** på kontot.

![Information om autentisering](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Registrera och konfigurera en Azure AD-app

1. I Azure-portalen väljer du Azure Active Directory**App-registreringar** > Ny registrering i listan över **Azure-tjänster** > i**Azure-portalen.**  

    ![Appregistrering](./media/how-to-manage-authentication/app-registration.png)

1. Om du redan har registrerat appen fortsätter du till nästa steg. Om du inte har registrerat appen anger du ett **namn,** väljer en **supportkontotyp**och väljer sedan **Registrera**.  

    ![Information om appregistrering](./media/how-to-manage-authentication/app-create.png)

1. Om du vill tilldela delegerade API-behörigheter till Azure Maps går du till programmet. Välj **API-behörigheter** > Under **Appregistreringar****.** Under **API:er som min organisation använder**söker du efter och väljer Azure **Maps**.

    ![Lägga till behörigheter för app-API](./media/how-to-manage-authentication/app-permissions.png)

1. Markera kryssrutan bredvid **Komma åt Azure Maps**och välj sedan Lägg till **behörigheter**.

    ![Välj behörigheter för app-API](./media/how-to-manage-authentication/select-app-permissions.png)

1. Slutför något av följande steg, beroende på din autentiseringsmetod. 

    * Om ditt program använder autentisering av användartoken med `oauth2AllowImplicitFlow`Azure Maps Web SDK aktiverar du . Om du vill aktivera det, i avsnittet `oauth2AllowImplicitFlow` **Manifest** i din appregistrering, inställd på true. 
    
       ![Appmanifest](./media/how-to-manage-authentication/app-manifest.png)

    * Om ditt program använder server- eller programautentisering går du till **Certifikat & hemligheter**på sidan för appregistrering. Ladda sedan upp ett offentligt nyckelcertifikat eller skapa ett lösenord genom att välja **Ny klienthemlighet**. 
    
       ![Skapa en klienthemlighet](./media/how-to-manage-authentication/app-keys.png)

        Om du skapar ett lösenord kopierar du lösenordet och lagrar det på ett säkert sätt när du har valt **Lägg till.** Du använder det här lösenordet för att hämta token från Azure AD.

       ![Lägga till en klienthemlighet](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Bevilja rollbaserad åtkomstkontroll till Azure Maps

När du har associerat ett Azure Maps-konto med din Azure AD-klient kan du bevilja åtkomstkontroll. Du beviljar *rollbaserad åtkomstkontroll* (RBAC) genom att tilldela en användare, grupp eller ett program till en eller flera Azure Maps-åtkomstkontrollroller. 

1. Gå till ditt **Azure Maps-konto**. Välj **Åtkomstkontroll (IAM)** > **Rolltilldelning**.

    ![Bevilja RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. Välj **Azure Maps Date Reader (Preview)** under **Roll**på fliken **Rolltilldelning** . Under **Tilldela åtkomst till**väljer du Azure **AD-användare, grupp eller tjänsthuvudnamn**. Välj användaren eller programmet. Välj sedan **Spara**.

    ![Lägg till rolltilldelning](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Visa tillgängliga RBAC-azure maps-roller

Om du vill visa RBAC-roller som är tillgängliga för Azure Maps går du till **Åtkomstkontroll (IAM)**. Välj **Roller**och sök sedan efter roller som börjar med *Azure Maps*. Dessa Azure Maps-roller är de roller som du kan bevilja åtkomst till.

![Visa tillgängliga roller](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Visa RBAC för Azure Maps

RBAC ger detaljerad åtkomstkontroll.

Om du vill visa användare och appar som har beviljats RBAC för Azure Maps går du till **Access Control (IAM)**. Där väljer du **Rolltilldelningar**och filtrerar sedan efter **Azure Maps**.

![Visa användare och appar som har beviljats RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Begär token för Azure Maps

När du har registrerat din app och associerat den med Azure Maps kan du begära åtkomsttoken.

Om ditt program använder autentisering av användartoken med Azure Maps Web SDK konfigurerar du html-sidan med Azure Maps-klient-ID och Azure AD-app-ID.

Om ditt program använder server- eller programautentisering, begär du `https://login.microsoftonline.com`en token från slutpunkten för Azure AD-token . Använd följande information i din begäran: 

* Azure AD-resurs-ID`https://atlas.microsoft.com/`
* Klient-ID för Azure Maps
* Azure AD-app-ID
* Lösenord eller certifikat för registrering av Azure AD-appar

| Azure-miljö   | Slutpunkt för Azure AD-token | Azure-resurs-ID |
| --------------------|-------------------------|-------------------|
| Offentligt Azure-moln        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government-molnet   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Mer information om hur du begär åtkomsttoken från Azure AD för användare och tjänsthuvudnamn finns i [Autentiseringsscenarier för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Nästa steg

Mer information finns i [Azure AD och Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Hitta API-användningsmåtten för ditt Azure Maps-konto:
> [!div class="nextstepaction"] 
> [Visa användningsstatistik](how-to-view-api-usage.md)

Utforska exempel som visar hur du integrerar Azure AD med Azure Maps:

> [!div class="nextstepaction"]
> [Exempel på Azure AD-autentisering](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
