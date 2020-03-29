---
title: Använd rollbaserad åtkomstkontroll för StorSimple | Microsoft-dokument
description: Beskriver hur du använder RBAC (Azure Role-based Access Control) i samband med StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2017
ms.author: alkohli
ms.openlocfilehash: a79753a897a62e194a759c23a9c0acc45c5f36c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66159134"
---
# <a name="role-based-access-control-for-storsimple"></a>Rollbaserad åtkomstkontroll för StorSimple

Den här artikeln innehåller en kort beskrivning av hur RBAC (Azure Role-Based Access Control) kan användas för din StorSimple-enhet. RBAC erbjuder finkornig åtkomsthantering för Azure. Använd RBAC för att ge precis rätt åtkomst till StorSimple-användarna för att göra sitt jobb istället för att ge alla obegränsad åtkomst. Mer information om grunderna för åtkomsthantering i Azure finns i [Komma igång med rollbaserad åtkomstkontroll i Azure-portalen](../role-based-access-control/overview.md).

Den här artikeln gäller StorSimple 8000-seriens enheter som kör Uppdatering 3.0 eller senare i Azure-portalen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-storsimple"></a>RBAC-roller för StorSimple

RBAC kan tilldelas baserat på rollerna. Rollerna säkerställer vissa behörighetsnivåer baserat på tillgängliga resurser i miljön. Det finns två typer av roller som StorSimple-användare kan välja mellan: inbyggd eller anpassad.

* **Inbyggda roller** – De inbyggda rollerna kan vara ägare, deltagare, läsare eller administratör för användaråtkomst. Mer information finns [i Inbyggda roller för Azure-rollbaserad åtkomstkontroll](../role-based-access-control/built-in-roles.md).

* **Anpassade roller** - Om de inbyggda rollerna inte passar dina behov kan du skapa anpassade RBAC-roller för StorSimple. Om du vill skapa en anpassad RBAC-roll börjar du med en inbyggd roll, redigerar den och importerar den sedan tillbaka i miljön. Hämtningen och överföringen av rollen hanteras med antingen Azure PowerShell eller Azure CLI. Mer information finns i [Skapa anpassade roller för rollbaserad åtkomstkontroll](../role-based-access-control/custom-roles.md).

Om du vill visa de olika roller som är tillgängliga för en StorSimple-enhetsanvändare i Azure-portalen går du till tjänsten StorSimple Device Manager och går sedan till **Access Control (IAM) > Roles**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Skapa en anpassad roll för StorSimple Infrastructure Administrator

I följande exempel börjar vi med den inbyggda **rollläsaren** som tillåter användare att visa alla resursomfattningar men inte redigera dem eller skapa nya. Vi utökar sedan den här rollen för att skapa en ny anpassad roll StorSimple Infrastructure admin. Den här rollen tilldelas användare som kan hantera infrastrukturen för StorSimple-enheterna.

1. Kör Windows PowerShell som administratör.

2. Logga in på Azure.

    `Connect-AzAccount`

3. Exportera reader-rollen som en JSON-mall på datorn.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Öppna JSON-filen i Visual Studio. Du ser att en typisk RBAC-roll består av tre huvudavsnitt, **Åtgärder,** **NotActions**och **AssignableScopes**.

    I avsnittet **Åtgärd** visas alla tillåtna åtgärder för den här rollen. Varje åtgärd tilldelas från en resursprovider. Använd resursleverantören för `Microsoft.StorSimple` en StorSimple-infrastrukturadministratör.

    Använd PowerShell för att se alla resursleverantörer som är tillgängliga och registrerade i din prenumeration.

    `Get-AzResourceProvider`

    Du kan också söka efter alla tillgängliga PowerShell-cmdletar för att hantera resursleverantörerna.

    I avsnitten **NotActions** visas alla begränsade åtgärder för en viss RBAC-roll. I det här exemplet är inga åtgärder begränsade.
    
    Under **AssignableScopes**visas prenumerations-ID:na. Kontrollera att RBAC-rollen innehåller det explicita prenumerations-ID där det används. Om rätt prenumerations-ID inte har angetts får du inte importera rollen i prenumerationen.

    Redigera filen med tanke på föregående överväganden.

    ```json
    {
        "Name":  "StorSimple Infrastructure Admin",
        "Id":  "<guid>",
        "IsCustom":  true,
        "Description":  "Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install, download etc.",
        "Actions":  [
                        "Microsoft.StorSimple/managers/alerts/read",
                        "Microsoft.StorSimple/managers/devices/volumeContainers/read",
                        "Microsoft.StorSimple/managers/devices/jobs/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/read",
                        "Microsoft.StorSimple/managers/devices/alertSettings/write",
                        "Microsoft.StorSimple/managers/clearAlerts/action",
                        "Microsoft.StorSimple/managers/devices/networkSettings/read",
                        "Microsoft.StorSimple/managers/devices/publishSupportPackage/action",
                        "Microsoft.StorSimple/managers/devices/scanForUpdates/action",
                        "Microsoft.StorSimple/managers/devices/metrics/read"

                    ],
        "NotActions":  [

                    ],
        "AssignableScopes":  [
                                "/subscriptions/<subscription_ID>/"
                            ]
    }
    ```

6. Importera den anpassade RBAC-rollen tillbaka till miljön.

    `New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Den här rollen ska nu visas i listan över roller i **bladet Åtkomstkontroll.**

![Visa RBAC-roller](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Mer information finns i [Anpassade roller](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Exempel på utdata för att skapa anpassade roller via PowerShell

```powershell
Connect-AzAccount
```

```Output
Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :
```

```powershell
Get-AzRoleDefinition -Name "Reader"
```

```Output
Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}
```

```powershell
Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
New-AzRoleDefinition -InputFile "C:\ssrbaccustom.json"
```

```Output
Name             : StorSimple Infrastructure Admin
Id               : <tenant_ID>
IsCustom         : True
Description      : Lets you view everything, but not make any changes except for Clear alerts, Clear settings, install,
                   download etc.
Actions          : {Microsoft.StorSimple/managers/alerts/read,
                   Microsoft.StorSimple/managers/devices/volumeContainers/read,
                   Microsoft.StorSimple/managers/devices/jobs/read,
                   Microsoft.StorSimple/managers/devices/alertSettings/read...}
NotActions       : {}
AssignableScopes : {/subscriptions/<subscription_ID>/}
```

## <a name="add-users-to-the-custom-role"></a>Lägga till användare i den anpassade rollen

Du beviljar åtkomst inifrån resursen, resursgruppen eller prenumerationen som rolltilldelningen omfattar. När du ger åtkomst bör du tänka på att åtkomsten som beviljas vid den överordnade noden ärvs av den underordnade. Mer information finns i [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md).

1. Gå till **åtkomstkontroll (IAM)**. Klicka på **+ Lägg till** på bladet För åtkomstkontroll.

    ![Lägga till åtkomst till RBAC-rollen](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Välj den roll som du vill tilldela, i det här fallet är det **StorSimple Infrastructure Admin**.

3. Välj den användare, den grupp eller det program i katalogen som du vill bevilja åtkomst till. Du kan söka i katalogen med visningsnamn, e-postadresser och objektidentifierare.

4. Välj **Spara** om du vill skapa tilldelningen.

    ![Lägga till behörigheter i RBAC-rollen](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

Ett **tilläggsanvändarmeddelande** spårar förloppet. När användaren har lagts till uppdateras listan över användare i Åtkomstkontrollen.

## <a name="view-permissions-for-the-custom-role"></a>Visa behörigheter för den anpassade rollen

När den här rollen har skapats kan du visa behörigheterna som är associerade med den här rollen i Azure-portalen.

1. Om du vill visa de behörigheter som är associerade med den här rollen går du till **IAM (Access control) > Roles > StorSimple Infrastructure Admin**. Listan över användare i den här rollen visas.

2. Välj en StorSimple Infrastructure Admin-användare och klicka på **Behörigheter**.

    ![Visa behörigheter för rollen StorSimple Infra Admin](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Behörigheterna som är associerade med den här rollen visas.

    ![Visa användare i rollen StorSimple Infra Admin](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [tilldelar anpassade roller för interna och externa användare](../role-based-access-control/role-assignments-external-users.md).
