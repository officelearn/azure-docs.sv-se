---
title: Rollbaserad Access Control i Azure (RBAC) för att kontrol lera åtkomst behörighet för att skapa och hantera support förfrågningar | Microsoft Docs
description: Rollbaserad Access Control i Azure (RBAC) för att kontrol lera åtkomst behörighet för att skapa och hantera support förfrågningar
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure-supportability
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: 94f21e973063dc627ef1424ca5393dd85aedd671
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2019
ms.locfileid: "72248843"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Rollbaserad Access Control i Azure (RBAC) för att kontrol lera åtkomst behörighet för att skapa och hantera support förfrågningar

[Rollbaserad Access Control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) möjliggör detaljerad åtkomst hantering för Azure.
Supportbegäran att skapa i Azure Portal, [Portal.Azure.com](https://portal.azure.com), använder Azures RBAC-modell för att definiera vem som kan skapa och hantera support förfrågningar.
Åtkomst beviljas genom att tilldela lämplig RBAC-roll till användare, grupper och program i ett visst omfång, som kan vara en prenumeration, en resurs grupp eller en resurs.

Låt oss ta ett exempel: som en resurs grupps ägare med Läs behörighet i prenumerations omfattningen, kan du hantera alla resurser under resurs gruppen, t. ex. webbplatser, virtuella datorer och undernät.
Men när du försöker skapa en support förfrågan mot den virtuella dator resursen, uppstår följande fel

![Prenumerations fel](./media/create-manage-support-requests-using-access-control/subscription-error.png)

I hanterings förfrågnings hantering behöver du Skriv behörighet eller en roll som har stöd åtgärden Microsoft. support/* i prenumerations omfattningen för att kunna skapa och hantera support förfrågningar.

I följande artikel förklaras hur du kan använda Azures anpassade rollbaserade Access Control (RBAC) för att skapa och hantera support förfrågningar i Azure Portal.

## <a name="getting-started"></a>Komma igång

I exemplet ovan skulle du kunna skapa en support förfrågan för resursen om du har tilldelat en anpassad RBAC-roll för prenumerationen av prenumerations ägaren.
[Anpassade RBAC-roller](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) kan skapas med hjälp av Azure PowerShell, kommando rads gränssnitt för Azure (CLI) och REST API.

Egenskapen åtgärder för en anpassad roll anger de Azure-åtgärder som rollen beviljar åtkomst till.
Om du vill skapa en anpassad roll för hantering av support förfrågningar måste rollen ha åtgärden Microsoft. support/*

Här är ett exempel på en anpassad roll som du kan använda för att skapa och hantera support förfrågningar.
Vi har gett den här rollen "Support Request Contributor" och hur vi refererar till den anpassade rollen i den här artikeln.

``` Json
{
    "Name":  "Support Request Contributor",
    "Id":  "1f2aad59-39b0-41da-b052-2fb070bd7942",
    "IsCustom":  true,
    "Description":  "Lets you create and manage support tickets.",
    "Actions":  [
                    "Microsoft.Support/*"
                ],
    "NotActions":  [
                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

Följ stegen som beskrivs i [den här videon](https://www.youtube.com/watch?v=-PaBaDmfwKI) för att lära dig hur du skapar en anpassad roll för din prenumeration.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Skapa och hantera support förfrågningar i Azure Portal

Låt oss ta ett exempel – du äger prenumerationen för Visual Studio MSDN.
Johan är din peer som är resurs ägare till några av resurs grupperna i den här prenumerationen och har Läs behörighet till prenumerationen.
Du vill ge åtkomst till din peer, Joe, möjlighet att skapa och hantera support biljetter för resurserna under den här prenumerationen.

1. Det första steget är att gå till prenumerationen. Under **Inställningar**visas en lista över användare. Välj användaren Johan, som har Läs behörighet för prenumerationen. Nu ska vi tilldela en ny anpassad roll till Joe.

    ![Lägg till roll](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Klicka på Lägg till under bladet "användare". Välj den anpassade rollen support förfrågan deltagare i listan över roller

    ![Lägg till stöd deltagar roll](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. När du har valt roll namnet klickar du på Lägg till användare och anger inloggnings uppgifter för Johan e-post. Klicka på ”Välj”

    ![Lägga till användare](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Klicka på OK för att fortsätta

    ![Lägg till åtkomst](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Nu ser du användaren med den nyligen tillagda anpassade rollen "support förfrågan deltagare" under prenumerationen som du är ägare till

    ![Användaren har lagts till](./media/create-manage-support-requests-using-access-control/user-added.png)

    När Johan loggar in på portalen, ser Johan den prenumeration som Joe lades till.

7. Johan klickar på "nytt Supportbegäran" från bladet "hjälp och support" och kan skapa support förfrågningar för "Visual Studio Ultimate med MSDN"

    ![Ny supportbegäran](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Om du klickar på alla support förfrågningar visas en lista över de support förfrågningar som skapats för den här prenumerationen ![Case information View @ no__t-1

## <a name="remove-support-request-access-in-the-azure-portal"></a>Ta bort support begär ande åtkomst i Azure Portal

Precis som det är möjligt att ge åtkomst till en användare för att skapa och hantera support förfrågningar, är det möjligt att även ta bort åtkomsten för användaren.

Om du vill ta bort möjligheten att skapa och hantera support förfrågningar går du till prenumerationen, klickar på Inställningar och klickar på användaren (i det här fallet Joe). Högerklicka på roll namnet, "support förfrågan deltagare" och klicka på "ta bort"

![Ta bort support begär ande åtkomst](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

När Johan loggar in på portalen och försöker skapa en supportbegäran, påträffas följande fel:

![Prenumerations fel-2](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Joe kan inte se några support förfrågningar när Joe väljer "alla support förfrågningar"

![vyn ärende Detaljer – 2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
