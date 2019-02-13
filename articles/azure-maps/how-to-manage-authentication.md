---
title: Hantera autentisering i Azure Maps | Microsoft Docs
description: Du kan använda Azure-portalen för att hantera autentisering i Azure Maps.
author: walsehgal
ms.author: v-musehg
ms.date: 02/09/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: db0450ab8b765ec158201c6db149a7de7d135b98
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56143909"
---
# <a name="manage-authentication-in-azure-maps"></a>Hantera autentisering i Azure Maps

När du har skapat ett Azure Maps-konto skapas tre GUID för Azure Active Directory (AD Azure) eller autentisering med delad nyckel.

Du kan hitta information om din autentisering genom att navigera till den **autentisering** sidan **inställningar** i Azure-portalen. Navigera till ditt konto. Välj sedan **autentisering** på menyn.


## <a name="view-authentication-details"></a>Visa autentisering-information

Om du vill visa information om din autentisering, navigera till den **autentisering** alternativ på inställningsmenyn.

![Visa autentisering](./media/how-to-manage-authentication/how-to-view-auth.png)

 Mer information om autentisering och Azure Maps, se [autentisering med Azure Maps](https://aka.ms/amauth).


## <a name="configure-azure-ad-app-registration"></a>Konfigurera Azure AD-Appregistrering

När en Azure Maps-konto har skapats, krävs en länk mellan din Azure AD-klient och Azure Maps Azure-resurs. 

1. Gå till AAD-bladet och skapa en Appregistrering med ett namn och logga in URL: en som öppnas startsidan för webbappen / API som ”https://localhost/”. Om du redan har en registrerad app kan du gå vidare till steg 2.

    ![Appregistrering](./media/how-to-manage-authentication/app-registration.png)

    ![Appregistrering](./media/how-to-manage-authentication/app-create.png)

2. Tilldela delegerade API-behörigheter till Azure Maps genom att gå till program under appregistreringar och klicka på **inställningar**.  Välj **nödvändiga behörigheter** och välj **Lägg till**. Sök och välj sedan Azure Maps under **Välj en API** och klicka på **Välj**.

    ![Api för appbehörigheter](./media/how-to-manage-authentication/app-permissions.png)

3. Slutligen under Select-behörigheter väljer åtkomst Azure Maps och klicka på Välj.

    ![Välj app api-behörigheter](./media/how-to-manage-authentication/select-app-permissions.png)

4. Följ steg en eller b, beroende på implementering av autentisering.

    1. Om programmet är avsikten är att använda token användarautentisering med vår Azure Maps Web SDK, måste du aktivera den `oauthEnableImplicitFlow` genom att ange den till true i avsnittet manifestet i detalj för appens registreringssida. 
    
       ![Appmanifestet](./media/how-to-manage-authentication/app-manifest.png)

    2. Om programmet använder servern/programmet autentiseringen går till den **nycklar** -bladet i appregistreringen och antingen skapa ett lösenord eller ladda upp ett offentligt nyckelcertifikat till registreringen. Om du skapar ett lösenord när du **spara**, kopiera lösenordet för senare, och lagra den på ett säkert sätt kan du använda detta för att hämta token från AAD. 

       ![Appnycklar](./media/how-to-manage-authentication/app-keys.png)


## <a name="grant-rbac-to-azure-maps"></a>Bevilja RBAC till Azure Maps

När ett Azure Maps-konto har associerats med Azure AD-klienten, kan du få åtkomstkontroll genom att tilldela användaren eller programmet till tillgängliga roller i Azure Maps åtkomstkontroll.

1. Gå till **Access Control** klickar du på **rolltilldelningar**, och **Lägg till rolltilldelning**.

    ![Bevilja RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. Rolltilldelning frigörs fönstret, Välj Azure Maps datum läsare (förhandsgranskning) **rollen**, **tilldela åtkomst till**: Azure AD-användare, grupp eller tjänstens huvudnamn, **Välj** användare eller program från listrutan och **spara**.

    ![Lägg till rolltilldelning](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Visa tillgängliga Azure Maps RBAC-roller

Om du vill visa tillgängliga rollbaserad roller för åtkomstkontroll för Azure Maps som kan bevilja åtkomst till, gå till **åtkomstkontroll (IAM)** klickar du på på **roller**, och Sök efter roller från och med **Azure Maps**.

![Visa tillgängliga roller](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-role-based-access-control-rbac"></a>Visa Azure Maps rollbaserad åtkomstkontroll (RBAC)

Azure AD skapar förutsättningar för detaljerad åtkomstkontroll via rollbaserad åtkomstkontroll (RBAC). 

Om du vill visa användare eller program som har beviljats RBAC för Azure Maps, gå till **åtkomstkontroll (IAM)** väljer **rolltilldelningar**, och filtrera efter **Azure Maps**. Alla tillgängliga roller visas nedan.

![Visa RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Begära token för Azure Maps

När din app har registrerats och som är associerade med Azure Maps, kan du begära åtkomst-token.

* Om programmet är avsikten är att använda token användarautentisering med vår Azure Maps Web SDK (webb), måste du konfigurera din html-sida med Azure Maps klient-ID och Azure AD App-ID.


* För program med servern/programmet autentisering måste begära en token från Azure AD-inloggningsslutpunkt https://login.microsoftonline.com med klient-ID för Azure Maps, Azure AD App-ID och Azure AD-App registrering av lösenord eller certifikat.

Läs mer på begäran om åtkomsttoken från Azure AD för användare och tjänsthuvudnamn [autentiseringsscenarier för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).


## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure AD-autentisering och Azure Maps Web SDK i [Azure AD och Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).