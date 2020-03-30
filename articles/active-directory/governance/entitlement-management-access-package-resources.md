---
title: Ändra resursroller för ett åtkomstpaket i Azure AD-berättigandehantering - Azure Active Directory
description: Lär dig hur du ändrar resursrollerna för ett befintligt åtkomstpaket i Azure Active Directory-berättigandehantering.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 102bbfbd1c02c93830f5c7fce89fe95d7fde54c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261897"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Ändra resursroller för ett åtkomstpaket i Azure AD-berättigandehantering

Som en åtkomstpakethanterare kan du ändra resurserna i ett åtkomstpaket när som helst utan att behöva oroa dig för att etablera användarens åtkomst till de nya resurserna eller ta bort deras åtkomst från de tidigare resurserna. I den här artikeln beskrivs hur du ändrar resursrollerna för ett befintligt åtkomstpaket.

Det här videoklippet innehåller en översikt över hur du ändrar ett åtkomstpaket.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Sök i katalogen efter resurser

Om du behöver lägga till resurser i ett åtkomstpaket bör du kontrollera om de resurser du behöver är tillgängliga i katalogen. Om du är en åtkomstpakethanterare kan du inte lägga till resurser i en katalog, även om du äger dem. Du är begränsad till att använda de resurser som är tillgängliga i katalogen.

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Katalog** på den vänstra menyn och öppna sedan katalogen.

1. Klicka på **Resurser** på den vänstra menyn om du vill visa listan över resurser i den här katalogen.

    ![Lista över resurser i en katalog](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Om du är en åtkomstpakethanterare och du behöver lägga till resurser i katalogen kan du be katalogägaren att lägga till dem.

## <a name="add-resource-roles"></a>Lägga till resursroller

En resursroll är en samling behörigheter som är associerade med en resurs. Det sätt du gör resurser tillgängliga för användare att begära är genom att lägga till resursroller i ditt åtkomstpaket. Du kan lägga till resursroller för grupper, team, program och SharePoint-webbplatser.

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket** på menyn till vänster och öppna sedan åtkomstpaketet.

1. Klicka på **Resursroller**på den vänstra menyn.

1. Klicka på **Lägg till resursroller** om du vill öppna sidan Lägg till resursroller för att komma åt paket.

    ![Åtkomstpaket - Lägg till resursroller](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. Beroende på om du vill lägga till en grupp, ett team, ett program eller en SharePoint-webbplats utför du stegen i något av följande resursrollavsnitt.

## <a name="add-a-group-or-team-resource-role"></a>Lägga till en grupp- eller gruppresursroll

Du kan låta rättighetshantering automatiskt lägga till användare i en grupp eller ett team i Microsoft Teams när de tilldelas ett åtkomstpaket. 

- När en grupp eller ett team ingår i ett åtkomstpaket och en användare tilldelas det åtkomstpaketet läggs användaren till i den gruppen eller gruppen, om det inte redan finns.
- När en användares tilldelning av åtkomstpaket upphör att gälla tas de bort från gruppen eller teamet, såvida de inte för närvarande har en tilldelning till ett annat åtkomstpaket som innehåller samma grupp eller grupp.

Du kan välja en [Azure AD-säkerhetsgrupp eller Office 365-grupp](../fundamentals/active-directory-groups-create-azure-portal.md). Administratörer kan lägga till valfri grupp i en katalog. katalogägare kan lägga till valfri grupp i katalogen om de är ägare till gruppen. Tänk på följande Azure AD-begränsningar när du väljer en grupp:

- När en användare, inklusive en gäst, läggs till som medlem i en grupp eller ett team kan de se alla andra medlemmar i gruppen eller teamet.
- Azure AD kan inte ändra medlemskapet i en grupp som synkroniserades från Active Directory i Windows Server med Azure AD Connect eller som skapades i Exchange Online som en distributionsgrupp.  
- Medlemskap i dynamiska grupper kan inte uppdateras genom att lägga till eller ta bort en medlem, så dynamiska gruppmedlemskap är inte lämpliga för användning med rättighetshantering.

Mer information finns i [Jämföra grupper](https://docs.microsoft.com/office365/admin/create-groups/compare-groups) och [Office 365-grupper och Microsoft Teams](https://docs.microsoft.com/microsoftteams/office-365-groups).

1. På sidan **Lägg till resursroller för att komma åt paket** klickar du på Grupper och **teams** för att öppna fönstret Välj grupper.

1. Välj de grupper och team som du vill inkludera i åtkomstpaketet.

    ![Åtkomstpaket - Lägg till resursroller - Välj grupper](./media/entitlement-management-access-package-resources/group-select.png)

1. Klicka på **Markera**.

    När du har valt gruppen eller gruppen visas en av följande undertyper i kolumnen **Undertyp:**

    |  |  |
    | --- | --- |
    | Säkerhet | Används för att bevilja åtkomst till resurser. |
    | Distribution | Används för att skicka meddelanden till en grupp personer. |
    | O365 | Office 365-grupp som inte är Teams-aktiverad. Används för samarbete mellan användare, både inom och utanför företaget. |
    | Team | Office 365-gruppen som är Teams-aktiverad. Används för samarbete mellan användare, både inom och utanför företaget. |

1. Välj **Ägare** eller Medlem **i** **rolllistan** .

    Du väljer vanligtvis rollen Medlem. Om du väljer rollen Ägare kan användare lägga till eller ta bort andra medlemmar eller ägare.

    ![Åtkomstpaket - Lägga till resursroll för en grupp eller ett team](./media/entitlement-management-access-package-resources/group-role.png)

1. Klicka på **Lägg till**.

    Alla användare med befintliga tilldelningar till åtkomstpaketet blir automatiskt medlemmar i den här gruppen eller teamet när det läggs till.

## <a name="add-an-application-resource-role"></a>Lägga till en programresursroll

Du kan låta Azure AD automatiskt tilldela användare åtkomst till ett Azure AD-företagsprogram, inklusive både SaaS-program och organisationens program som är federerade till Azure AD, när en användare tilldelas ett åtkomstpaket. För program som integreras med Azure AD via federerade enkel inloggning utfärdar Azure AD federationstoken för användare som tilldelats programmet.

Program kan ha flera roller. När du lägger till ett program i ett åtkomstpaket, om det programmet har mer än en roll, måste du ange lämplig roll för dessa användare. Om du utvecklar program kan du läsa mer om hur dessa roller läggs till i dina program i Så här konfigurerar du [rollanspråket som utfärdats i SAML-token för företagsprogram](../develop/active-directory-enterprise-app-role-management.md).

När en programroll är en del av ett åtkomstpaket:

- När en användare tilldelas det åtkomstpaketet läggs användaren till i programrollen, om den inte redan finns.
- När en användares åtkomstpakettilldelning upphör att gälla tas deras åtkomst bort från programmet, såvida de inte har en tilldelning till ett annat åtkomstpaket som innehåller programrollen.

Här är några saker när du väljer ett program:

- Program kan också ha grupper som tilldelats sina roller också.  Du kan välja att lägga till en grupp i stället för en programroll i ett åtkomstpaket, men då programmet inte kommer att vara synligt för användaren som en del av åtkomstpaketet i My Access-portalen.

1. På sidan **Lägg till resursroller för att komma åt paket** klickar du på **Program** för att öppna fönstret Välj program.

1. Välj de program som du vill inkludera i åtkomstpaketet.

    ![Åtkomstpaket - Lägg till resursroller - Välj program](./media/entitlement-management-access-package-resources/application-select.png)

1. Klicka på **Markera**.

1. Välj en programroll i **rolllistan.**

    ![Åtkomstpaket - Lägg till resursroll för ett program](./media/entitlement-management-access-package-resources/application-role.png)

1. Klicka på **Lägg till**.

    Alla användare med befintliga tilldelningar till åtkomstpaketet får automatiskt åtkomst till det här programmet när det läggs till.

## <a name="add-a-sharepoint-site-resource-role"></a>Lägga till en SharePoint-platsresursroll

Azure AD kan automatiskt tilldela användare åtkomst till en SharePoint Online-webbplats eller SharePoint Online-webbplatssamling när de tilldelas ett åtkomstpaket.

1. På sidan **Lägg till resursroller för att komma åt paketerar** du på **SharePoint-webbplatser** för att öppna fönstret Välj SharePoint Online-webbplatser.

1. Välj de SharePoint Online-webbplatser som du vill inkludera i åtkomstpaketet.

    ![Åtkomstpaket - Lägg till resursroller – Välj SharePoint Online-webbplatser](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Klicka på **Markera**.

1. Välj en SharePoint Online-webbplatsroll i **rolllistan.**

    ![Åtkomstpaket – Lägga till resursroll för en SharePoint Online-webbplats](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Klicka på **Lägg till**.

    Alla användare med befintliga tilldelningar till åtkomstpaketet får automatiskt åtkomst till den här SharePoint Online-webbplatsen när den läggs till.

## <a name="remove-resource-roles"></a>Ta bort resursroller

**Viktig roll:** Global administratör, användaradministratör, katalogägare eller åtkomstpakethanterare

1. Klicka på Azure **Active Directory** i Azure-portalen och klicka sedan på **Identitetsstyrning**.

1. Klicka på **Access-paket** på menyn till vänster och öppna sedan åtkomstpaketet.

1. Klicka på **Resursroller**på den vänstra menyn.

1. Leta reda på den resursroll som du vill ta bort i listan över resursroller.

1. Klicka på ellipsen (**...**) och klicka sedan på **Ta bort resursroll**.

    Alla användare med befintliga tilldelningar till åtkomstpaketet får automatiskt åtkomsten återkallad till den här resursrollen när den tas bort.

## <a name="when-changes-are-applied"></a>När ändringar tillämpas

I rättighetshantering bearbetar Azure AD massändringar för tilldelning och resurser i dina åtkomstpaket flera gånger om dagen. Så om du gör en tilldelning eller ändrar resursrollerna i ditt åtkomstpaket kan det ta upp till 24 timmar innan ändringen görs i Azure AD, plus hur lång tid det tar att sprida ändringarna till andra Microsoft Online-tjänster eller anslutna SaaS Program. Om ändringen påverkar bara några få objekt tar ändringen troligen bara några minuter att tillämpa i Azure AD, varefter andra Azure AD-komponenter identifierar ändringen och uppdaterar SaaS-programmen. Om ändringen påverkar tusentals objekt tar ändringen längre tid. Om du till exempel har ett åtkomstpaket med 2 program och 100 användartilldelningar och du bestämmer dig för att lägga till en SharePoint-webbplatsroll i åtkomstpaketet, kan det uppstå en fördröjning tills alla användare ingår i SharePoint-platsrollen. Du kan övervaka förloppet via Granskningsloggen för Azure AD, Azure AD-etableringsloggen och SharePoint-webbplatsgranskningsloggarna.

När du tar bort medlemmar från ett team tas de även bort från Office 365-gruppen. Borttagningen från teamets chattfunktioner kan vara fördröjd. Mer information finns i [Gruppmedlemskap](https://docs.microsoft.com/microsoftteams/office-365-groups#group-membership).

## <a name="next-steps"></a>Nästa steg

- [Skapa en basgrupp och lägga till medlemmar med hjälp av Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Så här konfigurerar du rollanspråket som utfärdas i SAML-token för företagsprogram](../develop/active-directory-enterprise-app-role-management.md)
- [Introduktion till SharePoint Online](/sharepoint/introduction)