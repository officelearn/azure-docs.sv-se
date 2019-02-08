---
title: Samarbeta med andra
titleSuffix: Language Understanding - Azure Cognitive Services
description: En appägare kan lägga till medarbetare till appen. Dessa medarbetare kan ändra modellen, träna och publicera appen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 4ccf703ffde0d4a5a0961135e81259519b0a0e41
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878977"
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

Om du använder Azure Active Directory (AD Azure) i din organisation, måste LUIS behörighet att komma åt-information om dina användare när de vill använda LUIS. Resurser som kräver att LUIS är minimal. 

Du kan se den detaljerade beskrivningen när du försöker registrera dig med ett konto som har administratörens godkännande eller inte kräver administratörens godkännande, till exempel administratörens godkännande:

* Kan du logga in i appen med ditt organisationskonto och tillåter appen att läsa din profil. Tillåter även appen att läsa grundläggande företagsinformation.
* Tillåter appen att visa och uppdatera dina data, även om du inte använder appen.

Den första behörigheten ger LUIS behörighet att läsa grundläggande profilinformation data, till exempel användar-ID, e-post, namn. Andra behörighet krävs för att uppdatera åtkomsttoken för användaren.

## <a name="azure-active-directory-tenant-user"></a>Användare med Azure Active Directory-klient

LUIS använder standard medgivande flödet för Azure Active Directory (AD Azure). 

Innehavaradministration bör arbeta direkt med användare som behöver åtkomst till använda LUIS i Azure AD. 

Först användaren loggar in på LUIS och ser popup-fönstret behöva godkännande av administratören. Användaren kontaktar administratör innan du fortsätter. 

Därefter innehavaradministration loggar in på LUIS och ser en dialogruta för medgivande-flödet. Det här är i dialogrutan som administratören behöver ge tillstånd för användaren. När administratören godkänner behörigheten, kan användaren fortsätta med LUIS.

Om innehavaradministration inte kommer att logga in på LUIS måste administratören kan komma åt [medgivande](https://account.activedirectory.windowsazure.com/r#/applications) för LUIS. 

![Azure active directory-behörighet genom appens webbplats](./media/luis-how-to-collaborate/tenant-permissions.png)

Om administratör vill bara vissa användare att använda LUIS, finns det några möjliga lösningar:
* Ge ”administratörsmedgivande” (godkänna alla användare av Azure AD), men ställs in på ”Ja” den ”Användartilldelning krävs” under Egenskaper för Enterprise-program och slutligen tilldela/Lägg till de önskade användarna till programmet. Med den här metoden kan administratören fortfarande tillhandahåller ”administratörsmedgivande” i appen, men det är möjligt att kontrollera de användare som har åtkomst till den.
* En andra lösningen är med hjälp av [Azure AD Graph API](https://docs.microsoft.com/graph/azuread-identity-access-management-concept-overview) att ge medgivande till varje specifik användare. 

Läs mer om Azure active directory-användare och godkännande: 
* [Begränsa din app](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md) till en uppsättning användare

### <a name="user-accounts-with-multiple-emails-for-collaborators"></a>Användarkonton med flera e-postmeddelanden för medarbetare

Om du lägger till medarbetare till en LUIS-app, anger du den exakta e-postadress som en medarbetare som behöver använda LUIS som medarbetare. Med Azure Active Directory (Azure AD) kan en enskild användare till flera e-postkonto som är utbytbara, måste användaren att logga in med den e-postadress som angetts i listan över den medarbetarens LUIS.

