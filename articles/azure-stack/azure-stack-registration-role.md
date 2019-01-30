---
title: Så här skapar du en roll för registrering för Azure Stack
description: Så här skapar du en anpassad roll för att undvika att använda global administratör för registrering.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 01/10/2019
ms.openlocfilehash: 80caa470675a78a9c2e3d4c055333719f54fe64a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247892"
---
# <a name="create-a-registration-role-for-azure-stack"></a>Skapa en roll för registrering för Azure Stack

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Scenarier där du inte vill ge behörigheter i Azure-prenumeration kan skapa du en anpassad roll om du vill tilldela behörigheter till ett användarkonto för att registrera din Azure Stack.

> [!WARNING]
> Detta är inte en säkerhetsfunktion för efterlevnadsstatus. Använda den i scenarier där du vill att restriktioner för att förhindra oavsiktliga ändringar till Azure-prenumeration. När en användare är delegerad behörighet till den här anpassade rollen, har användaren behörighet att redigera behörigheter och höjer rättigheter. Endast tilldela användare som du litar på den anpassade rollen.

När du registrerar Azure Stack kräver registrering-kontot i följande Azure Active Directory och behörigheter på Azure-prenumeration:

* **Registrering av behörigheter för programmet i din Azure Active Directory-klient:** Administratörer har behörigheter för registrering av programmet. Behörigheten för användare är en global inställning för alla användare i klienten. Visa eller ändra inställningen Se [och skapa en Azure AD program och tjänstens huvudnamn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

    Den *användare kan registrera program* måste ha angetts **Ja** om du vill aktivera ett användarkonto att registrera Azure Stack. Om inställningen appregistreringar anges till **nr**, du kan inte använda ett användarkonto och måste använda ett globalt administratörskonto för att registrera Azure Stack.

* **En uppsättning Azure-prenumeration behörighet:** Användare i gruppen ägare har tillräcklig behörighet. Du kan tilldela behörighetsuppsättningen genom att tilldela en anpassad roll som beskrivs i följande avsnitt för andra konton.

## <a name="create-a-custom-role-using-powershell"></a>Skapa en anpassad roll med hjälp av PowerShell

Om du vill skapa en anpassad roll måste du ha den `Microsoft.Authorization/roleDefinitions/write` behörighet på alla `AssignableScopes`, till exempel [ägare](../role-based-access-control/built-in-roles.md#owner) eller [administratör för användaråtkomst](../role-based-access-control/built-in-roles.md#user-access-administrator). Använd följande JSON-mallen för att förenkla definierar den anpassade rollen. Mallen skapar en anpassad roll som gör att de nödvändiga Läs- och skrivbehörighet för Azure Stack-registrering.

1. Skapa en JSON-fil. Exempel:  `C:\CustomRoles\registrationrole.json`
2. Lägg till följande JSON i filen. Ersätt <SubscriptionID> med ditt Azure-prenumerations-ID.

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. Anslut till Azure för att använda Azure Resource Manager i PowerShell. När du uppmanas, autentisera med ett konto med tillräcklig behörighet som [ägare](../role-based-access-control/built-in-roles.md#owner) eller [administratör för användaråtkomst](../role-based-access-control/built-in-roles.md#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Om du vill lägga till rollen till prenumerationen, använder **New-AzureRmRoleDefinition** att ange JSON-mallfil.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Tilldela en användare till rollen för registrering

När den anpassade rollen för registrering har skapats kan du tilldela rollen användarna registrera Azure Stack.

1. Logga in med kontot med behörighet på Azure-prenumeration för att delegera behörighet - exempelvis [ägare](../role-based-access-control/built-in-roles.md#owner) eller [administratör för användaråtkomst](../role-based-access-control/built-in-roles.md#user-access-administrator) .
2. I **prenumerationer**väljer **åtkomstkontroll (IAM) > Lägg till rolltilldelning**.
3. I **rollen**, väljer du den anpassa rollen som du skapade *Azure Stack-registrering rollen*.
4. Markera de användare som du vill tilldela till rollen.
5. Välj **spara** att tilldela de valda användarna till rollen.

    ![Välj användare ska tilldelas rollen](media/azure-stack-registration-role/assign-role.png)

Mer information om hur du använder anpassade roller finns i [hantera åtkomst med RBAC och Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Nästa steg

[Registrera Azure Stack med Azure](azure-stack-registration.md)
