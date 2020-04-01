---
title: Självstudiekurs - Skapa åtkomstpaket - Hantering av Azure AD-berättigande
description: Steg-för-steg-självstudiekurs för hur du skapar ditt första åtkomstpaket i Azure Active Directory-berättigandehantering.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: compliance
ms.date: 03/30/2020
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2d31ef46dfba31a8f217f68e8d5f98b67d58da5
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410606"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management"></a>Självstudiekurs: Skapa ditt första åtkomstpaket i Azure AD-berättigandehantering

Att hantera åtkomst till alla resurser som medarbetare behöver, till exempel grupper, program och webbplatser, är en viktig funktion för organisationer. Du vill ge medarbetarna rätt åtkomstnivå som de behöver för att vara produktiva och ta bort deras åtkomst när den inte längre behövs.

I den här självstudien arbetar du för Woodgrove Bank som IT-administratör. Du har ombetts att skapa ett paket med resurser för en marknadsföringskampanj som interna användare kan begära självbetjäning. Begäranden kräver inte godkännande och användarens åtkomst upphör att gälla efter 30 dagar. För den här självstudien är marknadsföringskampanjresurserna bara medlemskap i en enda grupp, men det kan vara en samling grupper, program eller SharePoint Online-webbplatser.

![Scenarioöversikt](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett åtkomstpaket med en grupp som resurs
> * Tillåt en användare i katalogen att begära åtkomst
> * Visa hur en intern användare kan begära åtkomstpaketet

En stegvis demonstration av processen för att distribuera Azure Active Directory-berättigandehantering, inklusive att skapa ditt första åtkomstpaket, visar du följande video:

>[!VIDEO https://www.youtube.com/embed/zaaKvaaYwI4]

## <a name="prerequisites"></a>Krav

Om du vill använda hantering av Azure AD-berättigande måste du ha någon av följande licenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-licens

Mer information finns i [Licenskrav](entitlement-management-overview.md#license-requirements).

## <a name="step-1-set-up-users-and-group"></a>Steg 1: Konfigurera användare och grupper

En resurskatalog har en eller flera resurser att dela. I det här steget skapar du en grupp med namnet **Marknadsföringsresurser** i Woodgrove Bank-katalogen som är målresursen för rättighetshantering. Du kan också ställa in en intern beställare.

**Viktig roll:** Global administratör eller användaradministratör

![Skapa användare och grupper](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Logga in på [Azure-portalen](https://portal.azure.com) som global administratör eller användaradministratör.  

1. Klicka på Azure **Active Directory**i den vänstra navigeringen .

1. Skapa eller konfigurera följande två användare. Du kan använda dessa namn eller olika namn. **Admin1** kan vara den användare som du för närvarande är inloggad som.

    | Namn | Katalogroll |
    | --- | --- |
    | **Admin1 (Admin1)** | Global administratör<br/>ELLER<br/>Användaradministratör |
    | **Beställare1** | Användare |

1. Skapa en Azure AD-säkerhetsgrupp med namnet **Marknadsföringsresurser** med en **medlemstyp av Tilldelad**.

    Den här gruppen kommer att vara målresursen för rättighetshantering. Gruppen ska vara tom på medlemmar som ska startas.

## <a name="step-2-create-an-access-package"></a>Steg 2: Skapa ett åtkomstpaket

Ett *åtkomstpaket* är en bunt resurser som en grupp eller ett projekt behöver och styrs med principer. Åtkomstpaket definieras i behållare som kallas *kataloger*. I det här steget skapar du ett åtkomstpaket **för marknadsföringskampanjer** i katalogen **Allmänt.**

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

![Skapa ett åtkomstpaket](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Klicka på Azure Active Directory i Azure-portalen i den vänstra **navigeringen**.

2. Klicka på **Identitetsstyrning** på den vänstra menyn

3. Klicka på **Access-paket**på menyn till vänster.  Om du ser **Nekad Åtkomst**kontrollerar du att det finns en Azure AD Premium P2-licens i katalogen.

4. Klicka på **Nytt åtkomstpaket**.

    ![Hantering av rättigheter i Azure-portalen](./media/entitlement-management-shared/access-packages-list.png)

5. På fliken Grunderna skriver du namnet **Marknadsföringskampanjåtkomstpaket** och beskrivning **Åtkomst till resurser för kampanjen**. **Basics**

6. Lämna listrutan **Katalog** inställd på **Allmänt**.

    ![Nytt åtkomstpaket - fliken Grunderna](./media/entitlement-management-access-package-first/basics.png)

7. Klicka på **Nästa** för att öppna fliken **Resursroller.**

    På den här fliken väljer du de resurser och den resursroll som ska ingå i åtkomstpaketet.

8. Klicka på **Grupper och lag**.

9. Leta reda på och välj gruppen **Marknadsföringsresurser som** du skapade tidigare i fönstret Välj grupper.

    Som standard visas grupper i och utanför katalogen **Allmänt.** När du väljer en grupp utanför katalogen **Allmänt** läggs den till i katalogen **Allmänt.**

    ![Nytt åtkomstpaket - fliken Resursroller](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

10. Klicka på **Markera** om du vill lägga till gruppen i listan.

11. Välj **Medlem**i listrutan **Roll** .

    ![Nytt åtkomstpaket - fliken Resursroller](./media/entitlement-management-access-package-first/resource-roles.png)

    >[!NOTE]
    > När du använder [dynamiska grupper](../users-groups-roles/groups-create-rule.md) ser du inga andra roller som är tillgängliga förutom ägare. Det här är avsiktligt.
    > ![Scenarioöversikt](./media/entitlement-management-access-package-first/dynamic-group-warning.png)

12. Klicka på **Nästa** för att öppna fliken **Begäranden.**

    På den här fliken skapar du en begärandeprincip. En *princip* definierar regler eller skyddsräcken för att komma åt ett åtkomstpaket. Du skapar en princip som gör att en viss användare i resurskatalogen kan begära det här åtkomstpaketet.

13. I avsnittet **Användare som kan begära åtkomst** klickar du på För användare i **katalogen** och sedan på **Specifika användare och grupper**.

    ![Nytt åtkomstpaket - fliken Begäranden](./media/entitlement-management-access-package-first/requests.png)

14. Klicka på **Lägg till användare och grupper**.

15. I fönstret Välj användare och grupper väljer du den **Requestor1-användare** som du skapade tidigare.

    ![Nytt åtkomstpaket - fliken Begäranden - Välj användare och grupper](./media/entitlement-management-access-package-first/requests-select-users-groups.png)

16. Klicka på **Markera**.

17. Bläddra ned till avsnitten **Godkännande** och **Aktivera begäranden.**

18. Lämna **Kräv godkännande** inställt på **Nr**.

19. För **Aktivera begäranden**klickar du på **Ja** för att aktivera det här åtkomstpaketet så snart det har skapats.

    ![Nytt åtkomstpaket - fliken Begäranden - Begäranden om godkännande och aktivera begäranden](./media/entitlement-management-access-package-first/requests-approval-enable.png)

20. Klicka på **Nästa** för att öppna fliken **Livscykel.**

21. I avsnittet **Förfallodatum** anger du **åtkomstpakettilldelningar som upphör** att gälla **antal dagar**.

22. Ange **att tilldelningar upphör** att gälla efter till **30** dagar.

    ![Nytt åtkomstpaket – fliken Livscykel](./media/entitlement-management-access-package-first/lifecycle.png)

23. Klicka på **Nästa** för att öppna fliken **Granska + Skapa.**

    ![Nytt åtkomstpaket - fliken Granska + Skapa](./media/entitlement-management-access-package-first/review-create.png)

    Efter en stund bör du se ett meddelande om att åtkomstpaketet har skapats.

24. Klicka på **Översikt**i menyn Marknadsföringskampanj i accesspaketet för marknadsföringskampanj .

25. Kopiera **länken Min Access-portal**.

    Du ska använda den här länken för nästa steg.

    ![Översikt över åtkomstpaket - Länken Min Access-portal](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-3-request-access"></a>Steg 3: Begärandeåtkomst

I det här steget utför du stegen som **den interna beställaren** och begär åtkomst till åtkomstpaketet. Beställare skickar sina begäranden med hjälp av en webbplats som kallas My Access-portalen. My Access-portalen gör det möjligt för beställare att skicka begäranden om åtkomstpaket, se de åtkomstpaket som de redan har åtkomst till och visa deras begäranhistorik.

**Viktig roll:** Intern beställare

1. Logga ut från Azure-portalen.

1. I ett nytt webbläsarfönster navigerar du till länken Min Åtkomstportal som du kopierade i föregående steg.

1. Logga in på My Access-portalen som **Requestor1**.

    Du bör se åtkomstpaketet **för marknadsföringskampanjer.**

1. Om det behövs klickar du på pilen i kolumnen **Beskrivning** för att visa information om åtkomstpaketet.

    ![My Access-portal - Access-paket](./media/entitlement-management-shared/my-access-access-packages.png)

1. Markera paketet genom att klicka på bocken.

1. Klicka på **Begär åtkomst** för att öppna fönstret Begär åtkomst.

    ![Knappen Min Åtkomstportal - Knappen Begär åtkomst](./media/entitlement-management-access-package-first/my-access-request-access-button.png)

1. I rutan **Affärsmotivering** skriver du den motivering **jag arbetar med den nya marknadsföringskampanjen**.

    ![My Access-portal - Åtkomst för begäran](./media/entitlement-management-shared/my-access-request-access.png)

1. Klicka på **Skicka**.

1. Klicka på Begär **historik** på den vänstra menyn för att kontrollera att din begäran har skickats.

## <a name="step-4-validate-that-access-has-been-assigned"></a>Steg 4: Verifiera att åtkomsten har tilldelats

I det här steget bekräftar du att den **interna beställaren** har tilldelats åtkomstpaketet och att de nu är medlemmar i gruppen **Marknadsföringsresurser.**

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Logga ut från My Access-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com) som **Admin1**.

1. Klicka på **Azure Active Directory** och sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket**på menyn till vänster.

1. Hitta och klicka på åtkomstpaketet **för marknadsföringskampanjer.**

1. Klicka på **Begäranden**på den vänstra menyn.

    Du bör se Requestor1 och den inledande principen med **statusen Levererad**.

1. Klicka på begäran om du vill se information om begäran.

    ![Åtkomstpaket - Information om begäran](./media/entitlement-management-access-package-first/request-details.png)

1. Klicka på Azure **Active Directory**i den vänstra navigeringen .

1. Klicka på **Grupper** och öppna gruppen **Marknadsföringsresurser.**

1. Klicka på **Medlemmar**.

    Du bör se **Requestor1** som medlem.

    ![Medlemmar i marknadsföringsresurser](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-5-clean-up-resources"></a>Steg 5: Rensa resurser

I det här steget tar du bort de ändringar du har gjort och tar bort åtkomstpaketet **för marknadsföringskampanjer.**

**Viktig roll:**  Global administratör eller användaradministratör

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Öppna åtkomstpaketet **för marknadsföringskampanjer.**

1. Klicka på **Tilldelningar**.

1. För **Requestor1**klickar du på ellipsen (**...**) och klickar sedan på Ta **bort åtkomst**. Klicka på **Ja**i meddelandet som visas .

    Efter en liten stund ändras statusen från Levererad till Utgången.

1. Klicka på **Resursroller**.

1. För **marknadsföringsresurser**klickar du på ellipsen (**...**) och klickar sedan på Ta **bort resursrollen**. Klicka på **Ja**i meddelandet som visas .

1. Öppna listan över åtkomstpaket.

1. För **marknadsföringskampanj**klickar du på ellipsen (**...**) och sedan på **Ta bort**. Klicka på **Ja**i meddelandet som visas .

1. I Active Directory i Azure tar du bort alla användare som du har skapat, till exempel **Requestor1** och **Admin1**.

1. Ta bort gruppen **Marknadsföringsresurser.**

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill veta mer om vanliga scenariosteg i rättighetshantering.
> [!div class="nextstepaction"]
> [Vanliga scenarier](entitlement-management-scenarios.md)
