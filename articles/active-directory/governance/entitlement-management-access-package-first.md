---
title: Självstudie – Skapa ditt första Access-paket i Azure AD-hantering (för hands version) – Azure Active Directory
description: Stegvis själv studie kurs om hur du skapar ditt första Access-paket i Azure Active Directory hantering av rättigheter (för hands version).
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
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1688651466ba6748e1254c9d33bb24435602868b
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489172"
---
# <a name="tutorial-create-your-first-access-package-in-azure-ad-entitlement-management-preview"></a>Självstudier: Skapa ditt första Access-paket i Azure AD-hantering av rättigheter (för hands version)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) rättighets hantering är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Att hantera åtkomst till alla resurser som krävs av de anställda, till exempel grupper, program och platser, är en viktig funktion för organisationer. Du vill ge anställda den rätta åtkomst nivån som de behöver för att vara produktiv och ta bort deras åtkomst när den inte längre behövs.

I den här självstudien arbetar du för Sparbanken-bank som IT-administratör. Du har blivit ombedd att skapa ett paket med resurser för ett webb projekt som interna användare kan själv betjäna. Begär Anden kräver godkännande och användarens åtkomst upphör att gälla efter 30 dagar. I den här självstudien är webb projekt resurserna bara medlemmar i en enda grupp, men det kan vara en samling av grupper, program eller SharePoint Online-webbplatser.

![Scenarioöversikt](./media/entitlement-management-access-package-first/elm-scenario-overview.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett Access-paket med en grupp som en resurs
> * Utse en god kännare
> * Visa hur en intern användare kan begära åtkomst paketet
> * Godkänn åtkomstbegäran

Om du inte har en Azure AD Premium P2-eller Enterprise Mobility + Security E5-licens skapar du en kostnads fri [Enterprise Mobility + Security E5-utvärderings version](https://signup.microsoft.com/Signup?OfferId=87dd2714-d452-48a0-a809-d2f58c4f68b7&ali=1).

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda Azure AD-hantering (för hands version) måste du ha en av följande licenser:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-licens

## <a name="step-1-set-up-users-and-group"></a>Steg 1: Konfigurera användare och grupp

En resurs katalog har en eller flera resurser att dela. I det här steget skapar du en grupp som heter **tekniker grupp** i katalogen Sparbanken som är mål resursen för hantering av rättigheter. Du skapar också en intern begär Ande.

**Nödvändig roll:** Global administratör eller användar administratör

![Skapa användare och grupper](./media/entitlement-management-access-package-first/elm-users-groups.png)

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör eller användar administratör.  

1. Klicka på **Azure Active Directory**i det vänstra navigerings fältet.

1. Skapa eller konfigurera följande två användare. Du kan använda dessa namn eller olika namn. **Admin1** kan vara den användare som du för närvarande är inloggad som.

    | Namn | Katalogroll | Beskrivning |
    | --- | --- | --- |
    | **Admin1** | Global administratör<br/>ELLER<br/>Begränsad administratör (användar administratör) | Administratör och god kännare |
    | **Requestor1** | Användare | Intern begär Ande |

    I den här självstudien är administratören och god kännaren samma person, men du väljer vanligt vis en eller flera personer som god kännare.

1. Skapa en Azure AD-säkerhetsgrupp med namnet **tekniker grupp** med en tilldelad medlemskaps typ.

    Den här gruppen är mål resursen för hantering av rättigheter. Gruppen ska vara tom för medlemmar att starta.

## <a name="step-2-create-an-access-package"></a>Steg 2: Skapa ett Access-paket

Ett *Access-paket* är ett paket med alla resurser som en användare behöver för att arbeta med ett projekt eller utföra sitt arbete. Åtkomst paket definieras i behållare som kallas *kataloger*. I det här steget skapar du ett **webb projekt åtkomst paket** i den **allmänna** katalogen.

**Nödvändig roll:** Global administratör eller användar administratör

![Skapa ett Access-paket](./media/entitlement-management-access-package-first/elm-access-package.png)

1. Klicka på **Azure Active Directory**i Azure Portal i det vänstra navigerings fältet.

1. På den vänstra menyn klickar du på **identitets styrning**

1. Klicka på **åtkomst paket**på den vänstra menyn.  Om du ser **nekad åtkomst**kontrollerar du att det finns en Azure AD Premium P2-licens i katalogen.

1. Klicka på **nytt Access-paket**.

    ![Hantering av rättigheter i Azure Portal](./media/entitlement-management-access-package-first/access-packages-list.png)

1. På fliken **grundläggande** anger du namnet på **webb projekt åtkomst paketet** och beskrivningen **för det tekniska webb projektet**.

1. Lämna List rutan **katalog** inställd på **Allmänt**.

    ![Nytt åtkomst paket – fliken grunder](./media/entitlement-management-access-package-first/basics.png)

1. Klicka på **Nästa** för att öppna fliken **resurs roller** .

    På den här fliken väljer du de behörigheter som ska tas med i åtkomst paketet.

1. Klicka på **grupper**.

1. I fönstret Välj grupper letar du reda på och väljer den **ingenjörs grupp** som du skapade tidigare.

    Som standard visas grupper i och utanför den **allmänna** katalogen. När du väljer en grupp utanför den **allmänna** katalogen kommer den att läggas till i den **allmänna** katalogen.

    ![Nytt Access-paket – fliken resurs roller](./media/entitlement-management-access-package-first/resource-roles-select-groups.png)

1. Klicka på **Välj** för att lägga till gruppen i listan.

1. I list rutan **roll** väljer du **medlem**.

    ![Nytt Access-paket – fliken resurs roller](./media/entitlement-management-access-package-first/resource-roles.png)

1. Klicka på **Nästa** för att öppna fliken **princip** .

1. Ange den **första principen** för att växla till **senare**.

    Du kommer att skapa principen i nästa avsnitt.

    ![Nytt åtkomst paket – fliken princip](./media/entitlement-management-access-package-first/policy.png)

1. Klicka på **Nästa** för att öppna fliken **Granska + skapa** .

    ![Nytt åtkomst paket – Granska + fliken Skapa](./media/entitlement-management-access-package-first/review-create.png)

1. Granska inställningarna för åtkomst paket och klicka sedan på **skapa**.

    Du kan se ett meddelande om att åtkomst paketet inte är synligt för användarna eftersom katalogen inte har Aktiver ATS än.

    ![Nytt åtkomst paket – inte synligt meddelande](./media/entitlement-management-access-package-first/not-visible.png)

1. Klicka på **OK**.

    Efter en liten stund bör du se ett meddelande om att Access-paketet har skapats.

## <a name="step-3-create-a-policy"></a>Steg 3: Skapa en princip

En *princip* definierar regler eller guardrails för åtkomst till ett Access-paket. I det här steget skapar du en princip som tillåter en speciell användare i resurs katalogen att begära åtkomst paketet. Du anger också att begär Anden måste godkännas och vem som ska bli god kännare.

![Skapa en princip för åtkomst paket](./media/entitlement-management-access-package-first/elm-access-package-policy.png)

**Nödvändig roll:** Global administratör eller användar administratör

1. I **webb projekt åtkomst paketet**går du till menyn till vänster och klickar på **principer**.

    ![Åtkomst till paket princip listan](./media/entitlement-management-access-package-first/policies-list.png)

1. Klicka på **Lägg till princip** för att öppna skapa princip.

1. Ange namnet på den **interna förfrågnings principen** och beskrivningen **tillåter användare i den här katalogen att begära åtkomst till webb projekt resurser**.

1. I avsnittet **användare som kan begära åtkomst** klickar du på **för användare i din katalog**.

    ![Skapa princip](./media/entitlement-management-access-package-first/policy-create.png)

1. Rulla ned till avsnittet **Välj användare och grupper** och klicka på **Lägg till användare och grupper**.

1. I fönstret Välj användare och grupper väljer du den **Requestor1** -användare som du skapade tidigare och klickar sedan på **Välj**.

1. I avsnittet **begäran** anger du **Kräv godkännande** till **Ja**.

1. I avsnittet **Välj god kännare** klickar du på **Lägg till god kännare**.

1. I fönstret Välj god kännare väljer du den **admin1** som du skapade tidigare och klickar sedan på **Välj**.

    I den här självstudien är administratören och god kännaren samma person, men du kan ange en annan person som god kännare.

1. I avsnittet **förfallo datum** går det att ange **åtkomst paket** till **antal dagar**.

1. Ange att **åtkomst upphör att gälla efter** **30** dagar.

1. Klicka på **Ja**för **att aktivera princip**.

    ![Skapa princip inställningar](./media/entitlement-management-access-package-first/policy-create-settings.png)

1. Klicka på **skapa** för att skapa den **interna förfrågnings principen**.

1. Klicka på **Översikt**i vänster meny i webb projektets åtkomst paket.

1. Kopiera **länken till min åtkomst Portal**.

    Du kommer att använda den här länken för nästa steg.

    ![Översikt över åtkomst paket – länken min åtkomst Portal](./media/entitlement-management-shared/my-access-portal-link.png)

## <a name="step-4-request-access"></a>Steg 4: Begär åtkomst

I det här steget utför du stegen som den **interna begär ande** och begär åtkomst till åtkomst paketet. Begär Anden skickar sina förfrågningar med hjälp av en plats som kallas min åtkomst Portal. På portalen My Access kan du skicka begär Anden om åtkomst till paket, se de åtkomst paket som de redan har åtkomst till och visa sin begär ande historik.

**Nödvändig roll:** Intern begär Ande

1. Logga ut från Azure Portal.

1. I ett nytt webbläsarfönster navigerar du till länken min åtkomst portal som du kopierade i föregående steg.

1. Logga in på portalen My Access som **Requestor1**.

    Du bör se **Access-paketet för webb projekt**.

1. Om det behövs klickar du på pilen i kolumnen **Beskrivning** för att visa information om Access-paketet.

    ![Åtkomst till Portal – åtkomst paket](./media/entitlement-management-shared/my-access-access-packages.png)

1. Klicka på bock markeringen för att välja paketet.

1. Klicka på **begär åtkomst** för att öppna fönstret begär åtkomst.

1. I rutan **affärs justering** anger du den motivering som används **för webb projekt**.

1. Ställ in **begäran för en viss period** växla till **Ja**.

1. Ange **Start datumet** till dagens datum och  slutdatumet till imorgon.

    ![Min åtkomst Portal – begär åtkomst](./media/entitlement-management-shared/my-access-request-access.png)

1. Klicka på **Skicka**.

1. På den vänstra menyn klickar du på **begär ande historik** för att kontrol lera att din begäran har skickats.

## <a name="step-5-approve-access-request"></a>Steg 5: Godkänn åtkomstbegäran

I det här steget loggar du in som **god kännare** -användare och godkänner åtkomstbegäran för den interna begär Ande. God kännare använder samma åtkomst portal som begär Anden som används för att skicka förfrågningar. Med hjälp av min åtkomst Portal kan god kännare Visa väntande godkännanden och godkänna eller Neka förfrågningar.

**Nödvändig roll:** Godkännare

1. Logga ut från min åtkomst Portal.

1. Logga in på [portalen My Access](https://myaccess.microsoft.com) som **admin1**.

1. Klicka på godkännanden på den vänstra menyn.

1. På fliken **väntande** söker du efter **Requestor1**.

    Om du inte ser begäran från Requestor1 väntar du några minuter och försöker igen.

1. Klicka på länken **Visa** för att öppna fönstret åtkomstbegäran.

1. Klicka på **Godkänn**.

1. Skriv orsaken till att du har **godkänt åtkomst för webb projekt**i rutan **orsak** .

    ![Min åtkomst Portal – åtkomstbegäran](./media/entitlement-management-shared/my-access-approve-request.png)

1. Klicka på **Skicka** för att skicka in ditt beslut.

    Du bör se ett meddelande om att det har godkänts.

## <a name="step-6-validate-that-access-has-been-assigned"></a>Steg 6: Verifiera att åtkomst har tilldelats

Nu när du har godkänt åtkomst förfrågan i det här steget bekräftar du att den **interna** beställaren har tilldelat åtkomst paketet och att de nu är medlem i gruppen **teknik grupp** .

**Nödvändig roll:** Global administratör eller användar administratör

1. Logga ut från min åtkomst Portal.

1. Logga in på [Azure Portal](https://portal.azure.com) som **admin1**.

1. Klicka på **Azure Active Directory** och klicka sedan på **identitets styrning**.

1. Klicka på **åtkomst paket**på den vänstra menyn.

1. Leta upp och klicka på **webb projekt åtkomst paket**.

1. I den vänstra menyn klickar du på **begär Anden**.

    Du bör se Requestor1 och den interna begär ande principen med statusen levererad .

1. Klicka på begäran om du vill visa information om begäran.

    ![Åtkomst paket – information om begäran](./media/entitlement-management-access-package-first/request-details.png)

1. Klicka på **Azure Active Directory**i det vänstra navigerings fältet.

1. Klicka på **grupper** och öppna gruppen **ingenjör** .

1. Klicka på **medlemmar**.

    Du bör se **Requestor1** som visas som en medlem.

    ![Teknik grupps medlemmar](./media/entitlement-management-access-package-first/group-members.png)

## <a name="step-7-clean-up-resources"></a>Steg 7: Rensa resurser

I det här steget ska du ta bort de ändringar du har gjort och ta bort åtkomst paketet för **åtkomst paket för webb projekt** .

**Nödvändig roll:**  Global administratör eller användar administratör

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. Öppna **Access-paket för webb projekt**.

1. Klicka på tilldelningar.

1. För **Requestor1**klickar du på ellipsen ( **...** ) och klickar sedan på **ta bort åtkomst**.

    Statusen kommer att ändras från levererat till upphör Ande.

1. Klicka på **principer**.

1. Klicka på ellipsen ( **...** ) och sedan på **ta bort**för en **intern princip för begär ande**.

1. Klicka på **resurs roller**.

1. För **teknisk grupp**klickar du på ellipsen ( **...** ) och klickar sedan på **ta bort resurs roll**.

1. Öppna listan med åtkomst paket.

1. För **Project Access-projekt**klickar du på ellipsen ( **...** ) och sedan på **ta bort**.

1. I Azure Active Directory tar du bort alla användare som du har skapat, till exempel **Requestor1** och **admin1**.

1. Ta bort **teknik grupps** gruppen.

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel och lär dig mer om vanliga scenario steg i hantering av rättigheter.
> [!div class="nextstepaction"]
> [Vanliga scenarier](entitlement-management-scenarios.md)
