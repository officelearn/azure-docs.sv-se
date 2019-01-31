---
title: Lägga till och ta bort användare för dynamiskt gruppmedlemskap – självstudie – Azure Active Directory
description: I den här självstudiekursen använder du grupper med användarmedlemskapsregler för att lägga till eller ta bort användare automatiskt
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.openlocfilehash: bf625b45fa47d4fa681521eb992bd2b07cc08946
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296255"
---
# <a name="tutorial-add-or-remove-group-members-automatically"></a>Självstudier: Lägga till eller ta bort gruppmedlemmar automatiskt

I Azure Active Directory (Azure AD) kan du automatiskt lägga till eller ta bort användare i säkerhetsgrupper eller Office 365-grupper, så att du inte alltid måste göra det manuellt. När egenskaper för en användare eller enhet ändras utvärderar Azure AD alla dynamiska gruppregler i klientorganisationen för att se om ändringen ska lägga eller ta bort medlemmar.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa en automatiskt ifylld grupp med gästanvändare från ett visst partnerföretag
> * Tilldela licenser till gruppen för partnerspecifika funktioner för gästanvändare för åtkomst
> * Bonus: skydda gruppen **Alla användare** genom att ta bort gästanvändare så att du, till exempel, kan ge medlemmarna användaråtkomst till webbplatser som är endast interna

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här funktionen kräver en Azure AD Premium-licens för dig som global administratör för klientorganisationen. Om du inte har en väljer du **Licenser** > **Produkter** > **Testa/Köp** i Azure AD.

Du behöver inte tilldela licenser till användarna för att de ska bli medlemmar i dynamiska grupper. Du behöver bara det minsta antalet tillgängliga Azure AD Premium P1-licenser i klientorganisationen för att täcka alla sådana användare. 

## <a name="create-a-group-of-guest-users"></a>Skapa en grupp med gästanvändare

Först skapar du en grupp för dina gästanvändare som alla finns i samma partnerföretag. De behöver en speciallicens, så det är ofta effektivare att skapa en grupp för det här syftet.

1. Logga in på Azure Portal https://portal.azure.com) med ett konto som är global administratör för klientorganisationen.
2. Välj **Azure Active Directory** > **Grupper** > **Ny grupp**.
  ![välj kommandot för ny grupp](./media/groups-dynamic-tutorial/new-group.png)
3. På bladet **Grupp**:
  
  * Välj **Säkerhet** som grupptyp
  * Ange `Guest users Contoso` som namn och beskrivning för gruppen
  * Ändra **Medlemstyp** till **Dynamisk användare**
  * Välj **Lägg till dynamisk fråga**
  
4. Välj **Avancerad regel** och ange följande i rutan **Avancerad regel**: `(user.userType -eq "Guest") -and (user.companyName -eq "Contoso")`
5. Välj **Lägg till fråga** för att stänga bladet.
6. På bladet **Grupp** väljer du **Skapa** för att skapa gruppen.

## <a name="assign-licenses"></a>Tilldela licenser

Nu när du har skapat din nya grupp kan du tillämpa licenserna som dessa partneranvändare behöver.

1. I Azure AD väljer du **Licenser**. Välj en eller flera licenser och välj sedan **Tilldela**.
2. Välj **Användare och grupper** och välj gruppen **Gästanvändare Contoso**, och spara dina ändringar.
3. Med **Tilldelningsalternativ** kan du aktivera eller inaktivera tjänstabonnemangen som innehåller de licenser du har valt. När du gör en ändring klickar du på **OK** för att spara dina ändringar.
4. Slutför tilldelningen genom att i fönstret **Tilldela licens** klicka på **Tilldela** längst ned i fönstret.

## <a name="remove-guests-from-all-users-group"></a>Ta bort gäster från gruppen Alla användare

Kanske är din slutliga plan att tilldela alla dina gästanvändare till egna grupper efter företag. Nu kan du även ändra gruppen **Alla användare** så att den reserveras för bara användare i din klientorganisation. Du kan sedan använda den till att tilldela appar och licenser som är specifika för din hemorganisation.

   ![Andra gruppen Alla användare till endast medlemmar](./media/groups-dynamic-tutorial/all-users-edit.png)

## <a name="clean-up-resources"></a>Rensa resurser

**Ta bort gästanvändargruppen**

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är global administratör för klientorganisationen.
2. Välj **Azure Active Directory** > **Grupper**. Välj gruppen **Gästanvändare Contoso**, välj ellipsen (...) och välj sedan **Ta bort**. När du tar bort gruppen tas alla tilldelade licenser bort.

**Återställa gruppen Alla användare**
1. Välj **Azure Active Directory** > **Grupper**. Välj namnet på gruppen **Alla användare** för att öppna gruppen.
1. Välj **Regler för dynamiskt medlemskap**, ta bort all text i regeln och välj **Spara**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]
> * Skapa en grupp med gästanvändare
> * Tilldela licenser till den nya gruppen
> * Andra gruppen Alla användare till endast medlemmar

Gå vidare till nästa artikel om du vill veta mer om grunderna i gruppbaserade licenser
> [!div class="nextstepaction"]
> [Grundläggande om grupplicenser](../fundamentals/active-directory-licensing-whatis-azure-portal.md)



