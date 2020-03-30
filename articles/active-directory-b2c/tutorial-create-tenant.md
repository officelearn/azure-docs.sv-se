---
title: Självstudiekurs - Skapa en Azure Active Directory B2C-klient
description: Lär dig hur du förbereder för att registrera dina program genom att skapa en Azure Active Directory B2C-klient med hjälp av Azure-portalen.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c31f3c4c6688af7d2142180e8d9b7100965bad96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186411"
---
# <a name="tutorial-create-an-azure-active-directory-b2c-tenant"></a>Självstudie: Skapar en Azure Active Directory B2C-klientorganisation

Innan dina program kan interagera med Azure Active Directory B2C (Azure AD B2C) måste de vara registrerade i en klient som du hanterar.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa en Azure AD B2C-klient
> * Länka din klient till din prenumeration
> * Växla till katalogen som innehåller din Azure AD B2C-klient
> * Lägga till Azure AD B2C-resursen som **favorit** i Azure-portalen

Du lär dig hur du registrerar ett program i nästa självstudiekurs.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="create-an-azure-ad-b2c-tenant"></a>Skapa en Azure AD B2C-klient

1. Logga in på [Azure-portalen](https://portal.azure.com/). Logga in med ett Azure-konto som har tilldelats minst [deltagarrollen](../role-based-access-control/built-in-roles.md) i prenumerationen eller en resursgrupp inom prenumerationen.

1. Välj den katalog som innehåller din prenumeration.

    I verktygsfältet Azure-portalen väljer du ikonen **Katalog + Prenumeration** och väljer sedan den katalog som innehåller din prenumeration. Den här katalogen skiljer sig från den som ska innehålla din Azure AD B2C-klient.

    ![Abonnemangsklient, Katalog + Prenumerationsfilter med prenumerationsklient vald](media/tutorial-create-tenant/portal-01-pick-directory.png)

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.
1. Sök efter **Azure Active Directory B2C**och välj sedan **Skapa**.
1. Välj **Skapa en ny Azure AD B2C-klient**.

    ![Skapa en ny Azure AD B2C-klient som valts i Azure-portalen](media/tutorial-create-tenant/portal-02-create-tenant.png)

1. Ange ett **organisationsnamn** och **ursprungligt domännamn**. Välj **land eller region** (det kan inte ändras senare) och välj sedan **Skapa**.

    Domännamnet används som en del av ditt fullständiga klientdomännamn. I det här exemplet är klientnamnet *contosob2c.onmicrosoft.com:*

    ![Skapa klientformulär i med exempelvärden i Azure Portal](media/tutorial-create-tenant/portal-03-tenant-naming.png)

1. När klientskapandet är klart väljer du länken **Skapa ny B2C-klientorganisation eller Länk till befintlig klientanslutning** högst upp på sidan för att skapa klient.

    ![Länk till klientlänklänken markerad i Azure-portalen](media/tutorial-create-tenant/portal-04-select-link-sub-link.png)

1. Välj **Länka en befintlig Azure AD B2C-klient till min Azure-prenumeration**.

   ![Länka ett befintligt prenumerationsval i Azure Portal](media/tutorial-create-tenant/portal-05-link-subscription.png)

1. Välj den **Azure AD B2C-klient som** du skapade och välj sedan din **prenumeration**.

    För **Resursgrupp** väljer du **Skapa ny**. Ange ett **namn** för resursgruppen som ska innehålla klienten, välj **resursgruppsplatsen**och välj sedan **Skapa**.

    ![Formuläret Länka prenumerationsinställningar i Azure Portal](media/tutorial-create-tenant/portal-06-link-subscription-settings.png)

    Du kan länka flera Azure AD B2C-klienter till en enda Azure-prenumeration för faktureringsändamål.

## <a name="select-your-b2c-tenant-directory"></a>Välj din B2C-klientkatalog

Om du vill börja använda din nya Azure AD B2C-klient måste du växla till katalogen som innehåller klienten.

Välj **katalog + prenumerationsfilter** i den övre menyn i Azure-portalen och välj sedan den katalog som innehåller din Azure AD B2C-klient.

Om du först inte ser din nya Azure B2C-klient i listan uppdaterar du webbläsarfönstret och väljer sedan **katalog + prenumerationsfiltret** igen i den övre menyn.

![B2C-katalog som innehåller klientvalt i Azure-portalen](media/tutorial-create-tenant/portal-07-select-tenant-directory.png)

## <a name="add-azure-ad-b2c-as-a-favorite-optional"></a>Lägg till Azure AD B2C som favorit (valfritt)

Det här valfria steget gör det enklare att välja din Azure AD B2C-klient i följande och alla efterföljande självstudier.

I stället för att söka efter *Azure AD B2C* i **Alla tjänster** varje gång du vill arbeta med din klientorganisation kan du i stället göra en favorit för resursen. Sedan kan du välja det från **portalmenyns favoritavsnitt** för att snabbt bläddra till din Azure AD B2C-klient.

Du behöver bara utföra den här åtgärden en gång. Innan du utför de här stegen kontrollerar du att du har bytt till katalogen som innehåller din Azure AD B2C-klient enligt beskrivningen i föregående avsnitt, [Välj din B2C-klientkatalog](#select-your-b2c-tenant-directory).

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster**på Portal-menyn i Azure portal .
1. Sök efter **Azure AD B2C**i sökrutan **Alla tjänster** , hovra över sökresultatet och välj sedan stjärnikonen i verktygstipset. **Azure AD B2C** visas nu i Azure-portalen under **Favoriter**.
1. Om du vill ändra positionen för din nya favorit går du till Azure-portalmenyn, väljer **Azure AD B2C**och drar den sedan uppåt eller nedåt till önskad position.

    ![Azure AD B2C, menyn Favoriter, Microsoft Azure-portalen](media/tutorial-create-tenant/portal-08-b2c-favorite.png)

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att:

> [!div class="checklist"]
> * Skapa en Azure AD B2C-klient
> * Länka din klient till din prenumeration
> * Växla till katalogen som innehåller din Azure AD B2C-klient
> * Lägga till Azure AD B2C-resursen som **favorit** i Azure-portalen

Läs sedan om hur du registrerar ett webbprogram i din nya klientorganisation.

> [!div class="nextstepaction"]
> [Registrera dina ansökningar >](tutorial-register-applications.md)
