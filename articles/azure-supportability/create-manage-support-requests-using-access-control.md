---
title: Azure rollbaserad åtkomstkontroll (RBAC) till åtkomstbehörighet att skapa och hantera supportärenden | Microsoft Docs
description: Azure rollbaserad åtkomstkontroll (RBAC) till åtkomstbehörighet att skapa och hantera supportärenden
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: c3557d54b9fb3fd0173eec1e327050ac7cc80fc2
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Azure rollbaserad åtkomstkontroll (RBAC) till åtkomstbehörighet att skapa och hantera supportärenden

[Rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) Aktivera detaljerad åtkomsthantering för Azure.
Stöd för begäran skapas i Azure-portalen [portal.azure.com](https://portal.azure.com), använder Azure RBAC-modellen för att definiera vem som kan skapa och hantera supportärenden.
Komma åt genom att tilldela rollen RBAC till användare, grupper och program för ett visst område som kan vara en prenumeration, resursgrupp eller resurs.

Låt oss ta ett exempel: du kan hantera alla resurser under resursgruppen som webbplatser, virtuella datorer och undernät som en resurs gruppägare med läsbehörighet i omfånget för prenumerationen.
Men när du försöker skapa en supportbegäran mot den virtuella datorresursen får du följande fel

![Prenumerationen fel](./media/create-manage-support-requests-using-access-control/subscription-error.png)

Stöd för hantering av begäran måste du skriva behörighet eller en roll som har stöd för åtgärden Microsoft.Support/* definitionsområdet prenumeration för att kunna skapa och hantera supportärenden.

I följande artikel som förklarar hur du kan använda Azures anpassade rollbaserad åtkomstkontroll (RBAC) för att skapa och hantera supportärenden i Azure-portalen.

## <a name="getting-started"></a>Komma igång

Använda exemplet ovan kan skulle du kunna skapa en supportförfrågan för din resurs om du har tilldelat en anpassad roll RBAC på prenumerationen av prenumerationsägaren.
[Anpassade RBAC-roller](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) kan skapas med hjälp av Azure PowerShell, Azure-kommandoradsgränssnittet (CLI) och REST-API.

Egenskapen åtgärder för en anpassad roll anger Azure operationer som rollen ger åtkomst.
Om du vill skapa en anpassad roll för stöd för hantering av begäran måste rollen ha åtgärden Microsoft.Support/*

Här är ett exempel på en anpassad roll som du kan använda för att skapa och hantera supportärenden.
Vi har med namnet ”Support begär bidragsgivare” för den här rollen och hur vi refererar till den anpassade rollen i den här artikeln.

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

Följ stegen som beskrivs i [den här videon](https://www.youtube.com/watch?v=-PaBaDmfwKI) att lära dig hur du skapar en anpassad roll för din prenumeration.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Skapa och hantera supportärenden i Azure-portalen

Låt oss ta ett exempel – du är ägare till prenumerationen ”Visual Studio MSDN-prenumeration”.
Joe är din peer som är en resursägare till vissa av resursgrupper i den här prenumerationen och har läsbehörighet för prenumerationen.
Du vill ge åtkomst till din peer, Johan, möjlighet att skapa och hantera supportärenden för resurser under den här prenumerationen.

1. Det första steget är att gå till prenumerationen och under ”inställningar” visas en lista med användare. Klicka på användare Joe som har reader åtkomst på prenumerationen och vi tilldela en ny anpassad roll i hubben.

    ![Lägg till roll](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Klicka på ”Lägg till” under bladet ”användare”. Välj den anpassade rollen som ”Support begär bidragsgivare” i listan över roller

    ![Lägga till stöd för deltagarrollen](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. När du har valt rollnamnet, klicka på ”Lägg till användare” och ange den Joe e-autentiseringsuppgifter. Klicka på ”Välj”

    ![Lägga till användare](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Klicka på ”Ok” om du vill fortsätta

    ![Lägg till åtkomst](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Nu ska du se användare med den nyligen tillagda anpassade rollen ”Support begär bidragsgivare” under den prenumeration som du är ägare

    ![Användare som läggs till](./media/create-manage-support-requests-using-access-control/user-added.png)

    När Joe loggar in på portalen, ser han prenumeration som han har lagts till.

7. Joe klickar du på ”en ny supportbegäran” från ”Hjälp och Support”-bladet och kan skapa supportärenden för ”Visual Studio Ultimate med MSDN”

    ![Ny supportbegäran](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Klicka på ”alla stöder begäranden” Joe kan se listan över supportärenden som skapats för den här prenumerationen ![fallet detaljvy](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Ta bort stöd för begäran om åtkomst i Azure-portalen

Det är möjligt att ta bort åtkomsten för användaren samt precis som det är möjligt att bevilja åtkomst till en användare att skapa och hantera supportärenden.
Klicka på ”inställningar” och användare (i det här fallet Joe) för att ta bort möjligheten att skapa och hantera supportärenden, gå till prenumerationen.
Högerklicka på rollen, ”Support begär bidragsgivare” och klicka på ”Ta bort”

![Ta bort stöd för begäran om åtkomst](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

När Joe loggar in på portalen och försök att skapa en supportbegäran, påträffar han felmeddelande

![Fel-2-prenumeration](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Johan kan inte se någon stöder begäranden när han klickar ”alla stöder begäranden”

![Case information vy-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
