---
title: Hantera autentisering i Azure Maps | Microsoft Docs
description: Du kan använda Azure-portalen för att hantera autentisering i Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 414ca7156fd59ec1dc08e45c73e9eb30835078d4
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259932"
---
# <a name="manage-authentication-in-azure-maps"></a>Hantera autentisering i Azure Maps

När du har skapat ett Azure Maps-konto skapas ett klient-ID och nycklar för Azure Active Directory (AD Azure) eller autentisering med delad nyckel.

## <a name="view-authentication-details"></a>Visa autentisering-information

Du kan visa information om din autentisering på Azure portal. Gå till ditt konto och välj **autentisering** på den **inställningar** menyn.

![Autentisering-information](./media/how-to-manage-authentication/how-to-view-auth.png)

 Mer information finns i [autentisering med Azure Maps](https://aka.ms/amauth).


## <a name="set-up-azure-ad-app-registration"></a>Konfigurera Azure AD app-registrering

När du skapar ett Azure Maps-konto måste du upprätta en anslutning mellan din Azure AD-klient och Azure Maps-resursen.

1. Gå till bladet Azure AD och skapa en appregistrering. Ange ett namn för registreringen. I den **inloggnings-URL** ange startsida webbappen / API (till exempel https:\//localhost/). Om du redan har en registrerad app, gå till steg 2.

    ![Appregistrering](./media/how-to-manage-authentication/app-registration.png)

    ![Information om registrering av appar](./media/how-to-manage-authentication/app-create.png)

2. Om du vill tilldela Azure Maps delegerade API-behörigheter, går du till programmet under **appregistreringar**, och välj sedan **inställningar**.  Välj **nödvändiga behörigheter**, och välj sedan **Lägg till**. Sök efter och välj **Azure Maps** under **Välj en API**, och välj sedan den **Välj** knappen.

    ![Programbehörigheter API](./media/how-to-manage-authentication/app-permissions.png)

3. Under **Välj behörigheter**väljer **åtkomst Azure Maps**, och välj sedan den **Välj** knappen.

    ![Välj app API-behörigheter](./media/how-to-manage-authentication/select-app-permissions.png)

4. I steget Slutför en eller b, beroende på autentiseringsmetod.

    1. Om programmet använder användartoken autentisering med Azure Maps Web SDK, aktivera `oauthEnableImplicitFlow` genom att ange den till true i avsnittet manifestet i detalj för appens registreringssida.
    
       ![Appmanifestet](./media/how-to-manage-authentication/app-manifest.png)

    2. Om ditt program använder servern/programmet autentisering går du till den **nycklar** -bladet i appregistrering och skapa ett lösenord eller ladda upp ett offentligt nyckelcertifikat till registreringen. Om du skapar ett lösenord när du har valt **spara**, kopiera lösenordet för senare och lagra den på ett säkert sätt. Du använder det här lösenordet för att hämta token från Azure AD.

       ![Appnycklar](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Bevilja RBAC till Azure Maps

När du har kopplat ett Azure Maps-konto med Azure AD-klienten, kan du ge åtkomstkontroll genom att tilldela en användare eller program till en eller flera Azure Maps roller för åtkomstkontroll.

1. Gå till **åtkomstkontroll (IAM)** väljer **rolltilldelningar**, och välj sedan **Lägg till rolltilldelning**.

    ![Bevilja RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. I den **Lägg till rolltilldelning** fönstret under **rollen**väljer **Azure Maps datum läsare (förhandsgranskning)**. Under **tilldela åtkomst till**väljer **Azure AD-användare, grupp eller tjänstens huvudnamn**. Under **Välj**, Välj önskade användare eller program. Välj **Spara**.

    ![Lägg till rolltilldelning](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Visa tillgängliga Azure Maps RBAC-roller

Om du vill visa roller för rollbaserad åtkomst åtkomstkontroll (RBAC) som är tillgängliga för Azure Maps går du till **åtkomstkontroll (IAM)** väljer **roller**, och sedan söka efter roller från och med **Azure Maps**. Det här är de roller som du kan bevilja åtkomst till.

![Visa tillgängliga roller](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Visa Azure Maps RBAC

RBAC ger detaljerad åtkomstkontroll.

Om du vill visa användare och appar som har beviljats RBAC för Azure Maps, gå till **åtkomstkontroll (IAM)** väljer **rolltilldelningar**, och sedan filtrera efter **Azure Maps**.

![Visa användare och appar som har beviljats RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Begära token för Azure Maps

När du registrera din app och associerat den med Azure Maps, kan du begära åtkomst-token.

* Om ditt program använder användartoken autentisering med Azure Maps Web SDK, måste du konfigurera din HTML-sida med klient-ID för Azure Maps och Azure AD app-ID.

* Om servern/programmet autentisering används i ditt program måste du begära en token från Azure AD-inloggningsslutpunkt `https://login.microsoftonline.com` med Azure AD-resurs-ID `https://atlas.microsoft.com/`, klient-ID för Azure Maps, Azure AD app-ID och Azure AD app-registrering lösenordet eller certifikatet.

Läs mer om hur du begär åtkomsttoken från Azure AD för användare och tjänsthuvudnamn [autentiseringsscenarier för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure AD-autentisering och Azure Maps Web SDK i [Azure AD och Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).