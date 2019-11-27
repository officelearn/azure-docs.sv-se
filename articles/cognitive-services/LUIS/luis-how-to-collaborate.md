---
title: Samar beta med andra – LUIS
titleSuffix: Azure Cognitive Services
description: En app-ägare kan lägga till deltagare till redigerings resursen. Dessa deltagare kan ändra modellen, träna och publicera appen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 96aa7517a3418ab30b0b6a1736eea950ecf4a731
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2019
ms.locfileid: "74533791"
---
# <a name="add-contributors-to-your-app"></a>Lägg till deltagare i din app

En app-ägare kan lägga till deltagare i appar. Dessa medarbetare kan ändra modellen, träna och publicera appen. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Lägg till deltagare i Azure Authoring-resursen

Följande procedur gäller för alla användare som har **migrerat** till att använda Azures redigerings resurs.

Du har migrerat om din LUIS redigerings miljö är knuten till en redigerings resurs på sidan för att **hantera > Azure-resurser** på Luis-portalen.

1. I Azure Portal letar du reda på den Language Understanding (LUIS) Authoring-resursen. Den har typen `LUIS.Authoring`.
1. På den här resursens **Access Control (IAM)** väljer du **+ Lägg till** och väljer sedan **Lägg till roll tilldelning**.

    ![I Azure Portal lägger du till roll tilldelning på redigerings resurs.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. I fönstret **Lägg till roll tilldelning** väljer du **rollen** deltagare. I alternativet **tilldela åtkomst till väljer du** **Azure AD-användare, grupp eller tjänstens huvud namn**. I alternativet **Välj** anger du användarens e-postadress. Om användaren är känd av fler än en e-postadress för samma domän kontrollerar du att ange det _primära_ e-postkontot.

    ![Lägg till användarens e-post till deltagar rollen för Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    När användarens e-postadress hittas väljer du kontot och väljer **Spara**. 

    Om du har problem med den här roll tilldelningen granskar du [Azure Role-tilldelningar](../../role-based-access-control/role-assignments-portal.md) och [fel sökning av Azure åtkomst kontroll](../../role-based-access-control/troubleshooting.md#problems-with-rbac-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Lägg till medarbetare i LUIS-appen

Följande procedur är för alla användare som inte har **migrerats** att använda Azures redigerings resurs.

Du har inte migrerat om din LUIS redigerings upplevelse inte är kopplad till en redigerings resurs på sidan för att **hantera > Azure-resurser** på Luis-portalen.

En app har en enda författare, ägare, men kan ha många medarbetare. Om du vill tillåta medarbetare att redigera LUIS-app, måste du lägga till e-postmeddelandet som de använder för att få åtkomst till LUIS-portalen i listan med medarbetare. När de läggs visar appen i sin LUIS-portal.

1. Välj **Hantera** på menyn längst upp till höger och välj sedan **medarbetare** på den vänstra menyn.

1. Välj **Lägg till medarbetare** i verktygsfältet.

1. Ange den e-postadress som medarbetaren använder för att logga in på LUIS-portalen.

    ![Lägg till medarbetares e-postadress](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Användare med flera e-postmeddelanden 

Om du lägger till deltagare/medarbetare i en LUIS-app, anger du den exakta e-postadressen. Medan Azure Active Directory (Azure AD) tillåter att en enskild användare har mer än ett e-postkonto som används interutbytbart, kräver LUIS att användaren loggar in med den e-postadress som anges när du lägger till deltagare/medarbetare.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory-resurser

Om du använder [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) i din organisation behöver language Understanding (Luis) behörighet till informationen om användarnas åtkomst när de vill använda Luis. Resurser som kräver att LUIS är minimal. 

Du kan se den detaljerade beskrivningen när du försöker registrera dig med ett konto som har administratörens godkännande eller inte kräver administratörens godkännande, till exempel administratörens godkännande:

* Kan du logga in i appen med ditt organisationskonto och tillåter appen att läsa din profil. Tillåter även appen att läsa grundläggande företagsinformation. Detta ger LUIS behörighet att läsa grundläggande profil data, t. ex. användar-ID, e-post, namn
* Tillåter appen att visa och uppdatera dina data, även om du inte använder appen. Behörigheten krävs för att uppdatera åtkomsttoken för användaren.


### <a name="azure-active-directory-tenant-user"></a>Användare med Azure Active Directory-klient

LUIS använder standard medgivande flödet för Azure Active Directory (AD Azure). 

Innehavaradministration bör arbeta direkt med användare som behöver åtkomst till använda LUIS i Azure AD. 

* Först användaren loggar in på LUIS och ser popup-fönstret behöva godkännande av administratören. Användaren kontaktar administratör innan du fortsätter. 
* Därefter innehavaradministration loggar in på LUIS och ser en dialogruta för medgivande-flödet. Det här är i dialogrutan som administratören behöver ge tillstånd för användaren. När administratören godkänner behörigheten, kan användaren fortsätta med LUIS. Om klient organisationen inte kommer att logga in på LUIS kan administratören komma åt [medgivande](https://account.activedirectory.windowsazure.com/r#/applications) för Luis, som visas i följande skärm bild. Observera att listan filtreras till objekt som innehåller namnet `LUIS`.

![Azure active directory-behörighet genom appens webbplats](./media/luis-how-to-collaborate/tenant-permissions.png)

Om klient administratören bara vill att vissa användare ska använda LUIS, finns det ett par möjliga lösningar:
* Ge "administrativt medgivande" (medgivande till alla användare av Azure AD), men Ställ sedan in på "Ja" "användar tilldelning krävs" under egenskaper för företags program och tilldela/Lägg slutligen till de önskade användarna i programmet. Med den här metoden ger administratören fortfarande "administrativt medgivande" till appen, men det är möjligt att kontrol lera vilka användare som har åtkomst till den.
* En andra lösning är att använda [API: et för identitets-och åtkomst hantering i Azure AD i Microsoft Graph](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) för att ge medgivande till varje enskild användare. 

Lär dig mer om Azure Active Directory-användare och medgivande: 
* [Begränsa din app](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) till en uppsättning användare

## <a name="next-steps"></a>Nästa steg

* Lär dig [hur du använder versioner](luis-how-to-manage-versions.md) för att kontrol lera appens livs cykel.
* Förstå begreppen, inklusive [redigering av resurser](luis-concept-keys.md#authoring-key) och [deltagare](luis-concept-keys.md#contributions-from-other-authors) på den resursen.
* Lär dig [hur du skapar](luis-how-to-azure-subscription.md) redigerings-och körnings resurser
* Migrera till den nya [redigerings resursen](luis-migration-authoring.md) 
