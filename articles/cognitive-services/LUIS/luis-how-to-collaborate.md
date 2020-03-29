---
title: Samarbeta med andra - LUIS
titleSuffix: Azure Cognitive Services
description: En appägare kan lägga till deltagare i redigeringsresursen. Dessa deltagare kan ändra modellen, träna och publicera appen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 913a2b26f67773d9fafbc0a8430d121fbabb97cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053446"
---
# <a name="add-contributors-to-your-app"></a>Lägga till deltagare i din app

En appägare kan lägga till deltagare i appar. Dessa medarbetare kan ändra modellen, träna och publicera appen. 

[!INCLUDE [Manage collaborators and contributors based on migrated or not-migrated apps](./includes/manage-contributor-collaborator-migration.md)]

## <a name="add-contributor-to-azure-authoring-resource"></a>Lägga till deltagare i Azure-redigeringsresurs

Följande procedur är för alla användare som har **migrerat** att använda Azure-redigeringsresursen.

Du har migrerat om luis-redigeringsupplevelsen är knuten till en redigeringsresurs på sidan **Hantera -> Azure-resurser** i LUIS-portalen.

1. Hitta tillfattningsresursen för språk understanding (LUIS) i Azure-portalen. Den har `LUIS.Authoring`typen .
1. På den här resursens **IAM-sida (Access Control)** väljer du **+Lägg till** och väljer sedan **Lägg till rolltilldelning**.

    ![Lägg till rolltilldelning på redigeringsresurs i Azure-portalen.](./media/luis-how-to-collaborate/authoring-resource-access-control-add-role.png)

1. Välj **rollen** som deltagare i fönstret **Lägg till rolltilldelning.** I alternativet **Tilldela åtkomst till** väljer du Azure **AD-användare, grupp eller tjänsthuvudnamn**. Ange användarens e-postadress i alternativet **Välj.** Om användaren är känd av mer än en e-postadress för samma domän kontrollerar du att du anger det _primära_ e-postkontot.

    ![Lägga till användarens e-post i deltagarrollen för Azure AD](./media/luis-how-to-collaborate/add-role-assignment-for-contributor.png)

    När användarens e-post hittas väljer du kontot och väljer **Spara**. 

    Om du har problem med den här rolltilldelningen kan du granska [Azure-rolltilldelningar](../../role-based-access-control/role-assignments-portal.md) och [felsökning av Azure-åtkomstkontroll](../../role-based-access-control/troubleshooting.md#problems-with-azure-role-assignments).

## <a name="add-collaborator-to-luis-app"></a>Lägga till medarbetare i LUIS-appen

Följande procedur är för alla användare som inte har **migrerat** att använda Azure-redigeringsresursen.

Du har inte migrerat om luis-redigeringsupplevelsen inte är knuten till en redigeringsresurs på sidan **Hantera -> Azure-resurser** i LUIS-portalen.

En app har en enda författare, ägaren, men kan ha många medarbetare. Om du vill tillåta medarbetare att redigera luis-appen måste du lägga till den e-postadress de använder för att komma åt LUIS-portalen i samarbetslistan. När de har lagts till visas appen i deras LUIS-portal.

1. Välj **Hantera** från den övre högra menyn och välj sedan **Medarbetare** i den vänstra menyn.

1. Välj **Lägg till medarbetare** i verktygsfältet.

1. Ange den e-postadress som medarbetaren använder för att logga in på LUIS-portalen.

    ![Lägga till medarbetarens e-postadress](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)


### <a name="users-with-multiple-emails"></a>Användare med flera e-postmeddelanden 

Om du lägger till deltagare/medarbetare i en LUIS-app anger du den exakta e-postadressen. Medan Azure Active Directory (Azure AD) tillåter en enskild användare att ha mer än ett e-postkonto som används omväxlande, kräver LUIS användaren att logga in med den e-postadress som anges när du lägger till bidragsgivaren / medarbetaren.

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory-resurser

Om du använder [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) i din organisation behöver SPRÅK UNDERSTANDING (LUIS) behörighet till informationen om användarnas åtkomst när de vill använda LUIS. De resurser som LUIS kräver är minimala. 

Du ser den detaljerade beskrivningen när du försöker registrera dig med ett konto som har administratörsmedgivande eller inte kräver administratörsmedgivande, till exempel administratörsmedgivande:

* Gör att du kan logga in på appen med ditt organisationskonto och låta appen läsa din profil. Det gör det också möjligt för appen att läsa grundläggande företagsinformation. Detta ger LUIS behörighet att läsa grundläggande profildata, till exempel användar-ID, e-post, namn
* Gör att appen kan se och uppdatera dina data, även när du inte använder appen för tillfället. Behörigheten krävs för att uppdatera åtkomsttoken för användaren.


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory-klientanvändare

LUIS använder standardflöde för Azure Active Directory (Azure AD). 

Klientadministratören bör arbeta direkt med den användare som behöver åtkomst beviljas för att använda LUIS i Azure AD. 

* Först loggar användaren in på LUIS och ser popup-dialogrutan som kräver administratörsgodkännande. Användaren kontaktar klientadministratören innan han fortsätter. 
* För det andra loggar klientadministratören in i LUIS och ser en popup-dialogruta för medgivandeflöde. Det här är dialogrutan som administratören behöver för att ge användaren behörighet. När administratören har accepterat behörigheten kan användaren fortsätta med LUIS. Om klientadministratören inte loggar in på LUIS kan administratören komma åt [medgivande](https://account.activedirectory.windowsazure.com/r#/applications) för LUIS, som visas i följande skärmbild. Observera att listan filtreras efter objekt `LUIS`som innehåller namnet .

![Azure active directory-behörighet efter appwebbplats](./media/luis-how-to-collaborate/tenant-permissions.png)

Om klientadministratören bara vill att vissa användare ska använda LUIS finns det ett par möjliga lösningar:
* Ge "administratörsmedgivande" (samtycke till alla användare av Azure AD), men ange sedan "Ja" den "Användartilldelning som krävs" under Egenskaper för Enterprise Application och slutligen tilldela/lägg till endast önskade användare i programmet. Med den här metoden tillhandahåller administratören fortfarande "administratörssamtycke" till appen, men det är möjligt att styra de användare som kan komma åt den.
* En andra lösning är att använda [Azure AD-identitets- och åtkomsthanterings-API:et i Microsoft Graph](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) för att ge samtycke till varje specifik användare. 

Läs mer om Azure-användare av Active Directory och samtycke: 
* [Begränsa appen](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) till en uppsättning användare

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [använder versioner](luis-how-to-manage-versions.md) för att styra appens livscykel.
* Förstå begreppen, inklusive [redigeringsresursen](luis-concept-keys.md#authoring-key) och [deltagarna](luis-concept-keys.md#contributions-from-other-authors) på den resursen.
* Lär dig hur du [skapar](luis-how-to-azure-subscription.md) redigerings- och körningsresurser
* Migrera till den nya [redigeringsresursen](luis-migration-authoring.md) 
