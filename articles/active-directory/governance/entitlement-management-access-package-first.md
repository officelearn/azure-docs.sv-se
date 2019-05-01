---
title: Självstudie – skapa din första åtkomst-paket i Azure AD-rättigheten hantering (förhandsversion) – Azure Active Directory
description: Stegvisa självstudier för hur du skapar ditt första åtkomst-paket i Azure Active Directory rättigheten hantering (förhandsversion).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 04/27/2019
ms.author: rolyon
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 354af736d5896214848205f41e429d9bf2c49863
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64873484"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>Självstudier: Skapa ditt första åtkomst-paket i Azure AD rättigheten hantering (förhandsversion)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättigheten management är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Hantera åtkomst till alla resurser anställda behovet av, till exempel grupper, program och webbplatser, är en viktig funktion för organisationer. Du vill ge anställda rätt åtkomstnivå som de behöver för att vara produktiva och deras åtkomst tas bort när den inte längre behövs.

I den här självstudien får arbeta du för Woodgrove Bank som IT-administratör. Du har blivit ombedd att skapa ett paket av resurser för ett webbprojekt som interna användare kan själva begäran. Begäranden som kräver godkännande och användarens åtkomst upphör att gälla efter 30 dagar. Projektet webbresurser är bara medlemskap i en grupp i den här självstudien, men det kan vara en uppsättning grupper, program eller SharePoint Online-platser.

![Scenarioöversikt](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en access-paket med en grupp som en resurs
> * Välja en godkännare
> * Visa hur en intern användare kan begära åtkomst-paketet
> * Godkänn åtkomstbegäran om

Om du inte har en Azure AD Premium P2 eller Enterprise Mobility + Security E5-licens kan du skapa en kostnadsfri [Enterprise Mobility + Security E5-utvärderingen](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill använda Azure AD rättigheten hantering (förhandsversion), måste du ha en av följande licenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 license

## <a name="step-1-set-up-users-and-group"></a>Steg 1: Konfigurera användare och grupper

En katalog i resursen har en eller flera resurser för att dela. I det här steget skapar du en grupp med namnet **Ingenjörsgruppen** i Woodgrove Bank-katalogen som är målresursen för berättigande management. Du också konfigurera en intern begärande.

**Nödvändiga roll:** Global administratör eller Användaradministratör

![Skapa användare och grupper](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Logga in på den [Azure-portalen](https://portal.azure.com) som en Global administratör eller Användaradministratör.  

1. I det vänstra navigeringsfönstret klickar du på **Azure Active Directory**.

1. Skapa eller konfigurera följande två användare. Du kan använda dessa namn eller ett annat namn. **Admin1** kan vara du för närvarande är inloggad som användaren.

    | Namn | Katalogroll | Beskrivning |
    | --- | --- | --- |
    | **Admin1** | Global administratör<br/>ELLER<br/>Begränsad administratör (Användaradministratör) | Administratörs- och godkännare |
    | **Requestor1** | Användare | Intern begärande |

    Den här självstudien administratör och godkännare är samma person, men du vanligtvis ange en eller flera personer ska vara godkännare.

1. Skapa en Azure AD-säkerhetsgrupp grupp med namnet **Ingenjörsgruppen** med en medlemskapstypen för **tilldelad**.

    Den här gruppen kommer att målresursen för berättigande management. Gruppen ska vara tom för medlemmar att starta.

## <a name="step-2-create-an-access-package"></a>Steg 2: Skapa en access-paket

En *åtkomst paketet* är ett paket med alla resurser som en användare behöver för att arbeta med ett projekt eller utföra sitt arbete. Åtkomst-paket har definierats i behållare som kallas *kataloger*. I det här steget skapar du en **webbpaket projekt åtkomst** i den **Allmänt** katalogen.

**Nödvändiga roll:** Global administratör eller Användaradministratör

![Skapa en access-paket](./media/entitlement-management-access-package-first/elm-access-package.png)

1. I Azure-portalen i det vänstra navigeringsfönstret klickar du på **Azure Active Directory**.

1. I den vänstra menyn klickar du på **Identitetsstyrning**

1. I den vänstra menyn klickar du på **åt paket**.  Om du ser **åtkomst nekad**, kontrollera att en Azure AD Premium P2-licens finns i den katalogen.

1. Klicka på **nya åtkomst paketet**.

    ![Berättigande hantering i Azure portal](./media/entitlement-management-access-package-first/access-packages-list.png)

1. På den **grunderna** Skriv namnet **webbpaket projekt åtkomst** och en beskrivning **åtkomst-paketet för webbprojektet Engineering**.

1. Lämna den **Catalog** listrutan inställd **Allmänt**.

    ![Nytt åtkomst-paket - grunderna fliken](./media/entitlement-management-access-package-first/basics.png)

1. Klicka på **nästa** att öppna den **resursroller** fliken.

    På den här fliken kan välja du behörigheterna som ska ingå i åtkomst-paketet.

1. Klicka på **grupper**.

1. I fönstret Välj grupper att söka efter och välja den **Ingenjörsgruppen** grupp som du skapade tidigare.

    Som standard kan du se grupper i och utanför den **Allmänt** katalogen. När du väljer en grupp utanför den **Allmänt** katalogen, läggs den till den **Allmänt** katalogen.

    ![Nytt åtkomst-paket – fliken för resurs-roller](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Klicka på **Välj** att lägga till gruppen i listan.

1. I den **rollen** listrutan, väljer **medlem**.

    ![Nytt åtkomst-paket – fliken för resurs-roller](./media/entitlement-management-access-package-first/resource-roles.png)

1. Klicka på **nästa** att öppna den **princip** fliken.

1. Ange den **skapa första princip** växla till **senare**.

    Du skapar principen i nästa avsnitt.

    ![Nytt åtkomst-paket - fliken](./media/entitlement-management-access-package-first/policy.png)

1. Klicka på **nästa** att öppna den **granska + skapa** fliken.

    ![Nytt åtkomst-paket – granska + skapa flik](./media/entitlement-management-access-package-first/review-create.png)

1. Granska åtkomstinställningar för paketet och klicka sedan på **skapa**.

    Du kan se ett meddelande om att åtkomst paketet inte synligt för användarna eftersom katalogen inte har aktiverats.

    ![Nytt åtkomst-paket - syns inte meddelande](./media/entitlement-management-access-package-first/not-visible.png)

1. Klicka på **OK**.

    Efter en liten stund bör du se ett meddelande om att åtkomst-paketet har skapats.

## <a name="step-3-create-a-policy"></a>Steg 3: Skapa en princip

En *princip* definierar regler eller guardrails att komma åt ett åtkomst-paket. I det här steget skapar du en princip som tillåter en viss användare i katalogen resurs att begära åtkomst till paketet. Du kan ange att begäranden måste godkännas och som kommer att godkännaren.

![Skapa en åtkomstprincip för paketet](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**Nödvändiga roll:** Global administratör eller Användaradministratör

1. I den **webbpaket projekt åtkomst**, i den vänstra menyn klickar du på **principer**.

    ![Principer för åtkomst paketlistan](./media/entitlement-management-access-package-first/policies-list.png)

1. Klicka på **Lägg till princip** att öppna skapa princip.

1. Skriv namnet **interna begärande princip** och en beskrivning **låter användare i den här katalogen för att begära åtkomst till projektet webbresurser**.

1. I den **användare som kan begära åtkomst** klickar du på **för användare i katalogen**.

    ![Skapa princip](./media/entitlement-management-access-package-first/policy-create.png)

1. Rulla ned till den **Välj användare och grupper** och klicka **lägga till användare och grupper**.

1. I Välj användare och grupper fönstret, Välj den **Requestor1** användare som du skapade tidigare och klicka sedan på **Välj**.

1. I den **begära** anger **kräver godkännande** till **Ja**.

1. I den **Välj godkännare** klickar du på **Lägg till godkännare**.

1. I fönstret Välj godkännare väljer du den **Admin1** du skapade tidigare och klicka sedan på **Välj**.

    Den här självstudien administratör och godkännare är samma person, men du kan ange en annan person som godkännare.

1. I den **upphör att gälla** anger **åtkomst paketet upphör att gälla** till **antalet dagar**.

1. Ange **åtkomst upphör att gälla efter** till **30** dagar.

1. För **aktiverar principen**, klickar du på **Ja**.

    ![Skapa principinställningar](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. Klicka på **skapa** att skapa den **interna begärande princip**.

1. I vänstra webbpaket projekt åtkomst-menyn klickar du på **översikt**.

1. Kopiera den **portalen Mina åtkomstlänk**.

    Du använder den här länken för nästa steg.

    ![Översikt över åtkomst paketet - portal Mina åtkomst-länk](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>Steg 4: Begär åtkomst

I det här steget ska du utföra stegen som den **interna begärande** och begära åtkomst till access-paketet. Beställare skicka sina begäranden med hjälp av en plats som heter min åtkomst-portalen. Mina åtkomst-portalen kan beställare att skicka en begäran om åtkomst paket finns i åtkomst-paket som de redan har åtkomst till och visa deras historik över ändringsförfrågningar.

**Nödvändiga roll:** Intern begärande

1. Logga ut från Azure-portalen.

1. Gå till Mina åtkomst portal länken som du kopierade i föregående steg i ett nytt webbläsarfönster.

1. Logga in på portalen för Mina åtkomst som **Requestor1**.

    Du bör se den **webbpaket projekt åtkomst**.

1. Om det behövs i den **beskrivning** kolumnen, klickar du på pilen för att visa information om åtkomst-paketet.

    ![Min åtkomstportalen - paket för åtkomst](./media/entitlement-management-shared/my-access-access-packages.png)

1. Klicka på bockmarkeringen för att välja paketet.

1. Klicka på **begär åtkomst** att öppna fönstret begäran om åtkomst.

1. I den **motivering** skriver anledningen **arbetar webbprojekt**.

1. Ange den **begäran för specifika perioden** växla till **Ja**.

1. Ange den **startdatum** till idag och **slutdatum** till imorgon.

    ![Min åtkomst portal – begär åtkomst](./media/entitlement-management-shared/my-access-request-access.png)

1. Klicka på **Skicka**.

1. I den vänstra menyn klickar du på **Förfrågningshistorik** att verifiera att din begäran har skickats.

## <a name="step-5-approve-access-request"></a>Steg 5: Godkänn åtkomstbegäran om

I det här steget ska du logga in som den **godkännaren** användaren och godkänna förfrågan för den interna begäranden. Godkännare använda samma Mina åtkomstportalen beställare att skicka begäran om hjälp. Med hjälp av Mina åtkomst-portal godkännare Visa väntande godkännanden och godkänna eller neka förfrågningar.

**Nödvändiga roll:** Godkännare

1. Logga ut från portalen Mina åtkomst.

1. Logga in på den [Mina åtkomstportalen](https://myaccess.microsoft.com) som **Admin1**.

1. I den vänstra menyn klickar du på **godkännanden**.

1. På den **väntande** fliken, hitta **Requestor1**.

    Om du inte ser begäran från Requestor1, Vänta några minuter och försök igen.

1. Klicka på den **visa** länk för att öppna fönstret för åtkomst-begäran.

1. Klicka på **godkänna**.

1. I den **orsak** skriver orsaken **godkänd åtkomst för webbprojekt**.

    ![Min åtkomstportalen - förfrågan](./media/entitlement-management-shared/my-access-approve-request.png)

1. Klicka på **skicka** att skicka ditt beslut.

    Du bör se ett meddelande om att det har har godkänts.

## <a name="step-6-validate-that-access-has-been-assigned"></a>Steg 6: Verifiera att åtkomst har tilldelats

Nu när du har godkänt förfrågan, i det här steget ska du bekräfta att den **interna begärande** har tilldelats åtkomst paketet och att de nu är medlem i den **Ingenjörsgruppen** grupp.

**Nödvändiga roll:** Global administratör eller Användaradministratör

1. Logga ut från portalen Mina åtkomst.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som den **Admin1**.

1. Klicka på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. I den vänstra menyn klickar du på **åt paket**.

1. Hitta och klickar på **webbpaket projekt åtkomst**.

1. I den vänstra menyn klickar du på **begäranden**.

    Du bör se Requestor1 och interna begärande-princip med statusen **levererade**.

1. Klicka på begäran för att se mer information.

    ![Paket för åtkomst - information om begäran](./media/entitlement-management-access-package-first/request-details.png)

1. I det vänstra navigeringsfönstret klickar du på **Azure Active Directory**.

1. Klicka på **grupper** och öppna den **Ingenjörsgruppen** grupp.

1. Klicka på **medlemmar**.

    Du bör se **Requestor1** visas som en medlem.

    ![Tekniker gruppmedlemmar](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>Steg 7: Rensa resurser

I det här steget ska du ta bort de ändringar du gjort och ta bort den **webbpaket projekt åtkomst** åtkomst paketet.

**Nödvändiga roll:**  Global administratör eller Användaradministratör

1. I Azure-portalen klickar du på **Azure Active Directory** och klicka sedan på **Identitetsstyrning**.

1. Öppna **webbpaket projekt åtkomst**.

1. Klicka på **tilldelningar**.

1. För **Requestor1**, klicka på ellipsen (**...** ) och klicka sedan på **ta bort åtkomst**.

    Status ändras från levererade till har upphört att gälla.

1. Klicka på **principer**.

1. För **interna begärande princip**, klicka på ellipsen (**...** ) och klicka sedan på **ta bort**.

1. Klicka på **resursroller**.

1. För **Ingenjörsgruppen**, klicka på ellipsen (**...** ) och klicka sedan på **ta bort resursrollen**.

1. Öppna listan över åtkomst paket.

1. För **webbprojekt projekt åtkomst**, klicka på ellipsen (**...** ) och klicka sedan på **ta bort**.

1. Ta bort alla användare som du skapade till exempel i Azure Active Directory, **Requestor1** och **Admin1**.

1. Ta bort den **Ingenjörsgruppen** grupp.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill veta mer om vanliga scenariot steg i rätt management.
> [!div class="nextstepaction"]
> [Vanliga scenarier](entitlement-management-scenarios.md)
