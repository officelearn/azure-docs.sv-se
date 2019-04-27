---
title: Azure rollbaserad åtkomstkontroll (RBAC) att kontrollera åtkomsträttigheterna att skapa och hantera supportärenden | Microsoft Docs
description: Azure rollbaserad åtkomstkontroll (RBAC) att kontrollera åtkomsträttigheterna att skapa och hantera supportförfrågningar
author: ganganarayanan
ms.author: gangan
ms.date: 1/31/2017
ms.topic: article
ms.service: azure
ms.assetid: 58a0ca9d-86d2-469a-9714-3b8320c33cf5
ms.openlocfilehash: d98d0637c6d520193b11f4267c59016772ef063a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809820"
---
# <a name="azure-role-based-access-control-rbac-to-control-access-rights-to-create-and-manage-support-requests"></a>Azure rollbaserad åtkomstkontroll (RBAC) att kontrollera åtkomsträttigheterna att skapa och hantera supportförfrågningar

[Rollbaserad åtkomstkontroll (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) Aktivera detaljerad åtkomsthantering för Azure.
Stöder skapande av begäran i Azure-portalen [portal.azure.com](https://portal.azure.com), använder Azure RBAC-modellen för att definiera vem som kan skapa och hantera supportförfrågningar.
Komma åt genom att tilldela lämpliga RBAC-roll till användare, grupper och program för ett visst omfång som kan vara en prenumeration, resursgrupp eller en resurs.

Låt oss ta ett exempel: Du kan hantera alla resurser i resursgruppen som webbplatser, virtuella datorer och undernät som en resursgruppägare med läsbehörigheter prenumerationsområde.
Men när du försöker skapa en supportbegäran mot den virtuella datorresursen uppstår följande fel

![Fel inträffade när prenumeration](./media/create-manage-support-requests-using-access-control/subscription-error.png)

Du måste skriva behörighet eller en roll som har stöd för åtgärden Microsoft.Support/* prenumerationsomfånget för att kunna skapa och hantera supportförfrågningar i stöd för hantering av begäran.

I följande artikel beskriver hur du kan använda Azures anpassade rollbaserad åtkomstkontroll (RBAC) för att skapa och hantera supportärenden i Azure-portalen.

## <a name="getting-started"></a>Komma igång

Med exemplet ovan kan skulle du kunna skapa en supportförfrågan för din resurs om du har tilldelats en anpassad RBAC-roll för prenumerationen med Prenumerationens ägare.
[Anpassad RBAC-roller](https://azure.microsoft.com/documentation/articles/role-based-access-control-custom-roles/) kan skapas med Azure PowerShell, Azure-kommandoradsgränssnittet (CLI) och REST-API.

Egenskapen åtgärder för en anpassad roll anger de Azure-åtgärder som rollen ger åtkomst.
Om du vill skapa en anpassad roll för stöd för hantering av begäran måste rollen ha åtgärd Microsoft.Support/*

Här är ett exempel på en anpassad roll som du kan använda för att skapa och hantera supportförfrågningar.
Vi har med namnet ”Support begära bidragsgivare” för den här rollen och hur vi refererar till den anpassade rollen i den här artikeln.

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

Följ stegen som beskrivs i [videon](https://www.youtube.com/watch?v=-PaBaDmfwKI) att lära dig hur du skapar en anpassad roll för din prenumeration.

## <a name="create-and-manage-support-requests-in-the-azure-portal"></a>Skapa och hantera supportförfrågningar i Azure portal

Låt oss ta ett exempel – du är ägare av prenumerationen ”Visual Studio MSDN-prenumeration.
Jens peerenheten som är en resursägare till vissa av resursgrupper i den här prenumerationen och har läsbehörighet till prenumerationen.
Du vill ge åtkomst till din peer Joe, möjligheten att skapa och hantera supportärenden för resurserna under den här prenumerationen.

1. Det första steget är att gå till prenumerationen och under ”inställningar” visas en lista över användare. Klicka på användare Joe som har läsåtkomst på prenumerationen och vi tilldela en ny anpassad roll för honom.

    ![Lägg till roll](./media/create-manage-support-requests-using-access-control/add-role.png)

2. Klicka på ”Lägg till” under ”användare”-bladet. Välj den anpassade rollen ”Support begära bidragsgivare” i listan över roller

    ![Lägg till stöd för deltagarrollen](./media/create-manage-support-requests-using-access-control/add-support-contributor-role.png)

3. När du har valt rollnamnet, klicka på ”Lägg till användare” och ange den Joe e-autentiseringsuppgifter. Klicka på ”Välj”

    ![Lägga till användare](./media/create-manage-support-requests-using-access-control/add-users.png)

4. Klicka på ”Ok” för att fortsätta

    ![Lägg till åtkomst](./media/create-manage-support-requests-using-access-control/add-access.png)

5. Du nu se användare med den nya anpassade rollen ”Support begära bidragsgivare” i prenumeration för vilken du är ägare

    ![Användare har lagts till](./media/create-manage-support-requests-using-access-control/user-added.png)

    När Joe loggar in på portalen, ser han den prenumeration som han har lagts till.

7. Johan klickar du på ”ny supportförfrågan” som från bladet ”hjälp och Support” och kan skapa supportärenden för ”Visual Studio Ultimate med MSDN”

    ![Ny supportbegäran](./media/create-manage-support-requests-using-access-control/new-support-request.png)

8. Klicka på ”alla supportärenden” Johan kan se en lista över supportärenden som skapats för den här prenumerationen ![fallet detaljvy](./media/create-manage-support-requests-using-access-control/case-details-view.png)

## <a name="remove-support-request-access-in-the-azure-portal"></a>Ta bort stöd för begäran om åtkomst i Azure portal

Det är möjligt att ta bort åtkomst för användaren samt precis som det är möjligt att bevilja åtkomst till en användare att skapa och hantera supportförfrågningar.
Om du vill ta bort möjligheten att skapa och hantera supportärenden, gå till prenumerationen, klicka på ”inställningar” och klicka på användaren (i det här fallet Joe).
Högerklicka på namnet på rollen, ”Support begära bidragsgivare” och klicka på ”Ta bort”

![Ta bort tillgången till support-begäran](./media/create-manage-support-requests-using-access-control/remove-support-request-access.png)

När Joe loggar in på portalen och försöker att skapa en supportförfrågan, uppstår han följande fel

![Fel-2-prenumerationen](./media/create-manage-support-requests-using-access-control/subscription-error-2.png)

Johan kan inte se någon supportförfrågningar när han klickar på ”alla supportärenden”

![ärendeinformation vy-2](./media/create-manage-support-requests-using-access-control/case-details-view-2.png)
