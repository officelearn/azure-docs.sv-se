---
title: Samar beta med andra – LUIS
titleSuffix: Azure Cognitive Services
description: En appägare kan lägga till medarbetare till appen. Dessa medarbetare kan ändra modellen, träna och publicera appen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 2aaedb27d6875335b34c757505981f1a58462541
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932827"
---
# <a name="how-to-manage-authors-and-collaborators"></a>Så här hanterar du författare och medarbetare 

En appägare kan lägga till medarbetare till appen. Dessa medarbetare kan ändra modellen, träna och publicera appen. 

<a name="owner-and-collaborators"></a>

## <a name="add-collaborator"></a>Lägg till medarbetare

En app har en enda författare, ägare, men kan ha många medarbetare. Om du vill tillåta medarbetare att redigera LUIS-app, måste du lägga till e-postmeddelandet som de använder för att få åtkomst till LUIS-portalen i listan med medarbetare. När de läggs visar appen i sin LUIS-portal.

1. Välj **hantera** från den övre högra menyn, Välj **medarbetare** på den vänstra menyn.

2. Välj **Lägg till medarbetare** från verktygsfältet.

    [![Lägg till medarbetare](./media/luis-how-to-collaborate/add-collaborator.png "Lägg till medarbetare")](./media/luis-how-to-collaborate/add-collaborator.png#lightbox)

3. Ange den e-postadress som medarbetaren använder för att logga in på LUIS-portalen.

    ![Lägg till medarbetares e-postadress](./media/luis-how-to-collaborate/add-collaborator-pop-up.png)

## <a name="transfer-of-ownership"></a>Överlåtelse av äganderätt

Medan LUIS inte stöder för närvarande överlåtelse av äganderätt, du kan exportera din app och en annan LUIS-användare kan importera appen. Det kan finnas mindre skillnader i LUIS poäng mellan de två programmen. 

## <a name="azure-active-directory-resources"></a>Azure Active Directory-resurser

Om du använder [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) (Azure AD) i din organisation behöver language Understanding (Luis) behörighet till informationen om användarnas åtkomst när de vill använda Luis. Resurser som kräver att LUIS är minimal. 

Du kan se den detaljerade beskrivningen när du försöker registrera dig med ett konto som har administratörens godkännande eller inte kräver administratörens godkännande, till exempel administratörens godkännande:

* Kan du logga in i appen med ditt organisationskonto och tillåter appen att läsa din profil. Tillåter även appen att läsa grundläggande företagsinformation. Detta ger LUIS behörighet att läsa grundläggande profil data, t. ex. användar-ID, e-post, namn
* Tillåter appen att visa och uppdatera dina data, även om du inte använder appen. Behörigheten krävs för att uppdatera åtkomsttoken för användaren.


## <a name="azure-active-directory-tenant-user"></a>Användare med Azure Active Directory-klient

LUIS använder standard medgivande flödet för Azure Active Directory (AD Azure). 

Innehavaradministration bör arbeta direkt med användare som behöver åtkomst till använda LUIS i Azure AD. 

* Först användaren loggar in på LUIS och ser popup-fönstret behöva godkännande av administratören. Användaren kontaktar administratör innan du fortsätter. 
* Därefter innehavaradministration loggar in på LUIS och ser en dialogruta för medgivande-flödet. Det här är i dialogrutan som administratören behöver ge tillstånd för användaren. När administratören godkänner behörigheten, kan användaren fortsätta med LUIS. Om klient organisationen inte kommer att logga in på LUIS kan administratören komma åt [medgivande](https://account.activedirectory.windowsazure.com/r#/applications) för Luis, som visas i följande skärm bild. Observera att listan filtreras till objekt som innehåller namnet `LUIS`.

![Azure active directory-behörighet genom appens webbplats](./media/luis-how-to-collaborate/tenant-permissions.png)

Om klient administratören bara vill att vissa användare ska använda LUIS, finns det ett par möjliga lösningar:
* Ge "administrativt medgivande" (medgivande till alla användare av Azure AD), men Ställ sedan in på "Ja" "användar tilldelning krävs" under egenskaper för företags program och tilldela/Lägg slutligen till de önskade användarna i programmet. Med den här metoden ger administratören fortfarande "administrativt medgivande" till appen, men det är möjligt att kontrol lera vilka användare som har åtkomst till den.
* En andra lösning är att använda [Azure AD-Graph API](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) för att ge medgivande till varje enskild användare. 

Lär dig mer om Azure Active Directory-användare och medgivande: 
* [Begränsa din app](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) till en uppsättning användare

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Användarkonton med flera e-postmeddelanden för medarbetare

Om du lägger till medarbetare till en LUIS-app, anger du den exakta e-postadress som en medarbetare som behöver använda LUIS som medarbetare. Med Azure Active Directory (Azure AD) kan en enskild användare till flera e-postkonto som är utbytbara, måste användaren att logga in med den e-postadress som angetts i listan över den medarbetarens LUIS.

