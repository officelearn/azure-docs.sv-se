---
title: Använd rollbaserad åtkomstkontroll för StorSimple | Microsoft Docs
description: Beskriver hur du använder Azure rollbaserad åtkomstkontroll (RBAC) i samband med StorSimple.
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
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2019
ms.locfileid: "59049480"
---
# <a name="role-based-access-control-for-storsimple"></a>Rollbaserad åtkomstkontroll för StorSimple

Den här artikeln innehåller en kort beskrivning av hur rollbaserad åtkomstkontroll (RBAC) kan användas för StorSimple-enheten. RBAC ger detaljerad åtkomsthantering för Azure. Använd RBAC för att bevilja bara rätt mängd StorSimple-användare att utföra sitt arbete istället för att ge alla tillgång till obegränsad åtkomst. Mer information om grunderna för åtkomsthantering i Azure finns i [Kom igång med rollbaserad åtkomstkontroll i Azure-portalen](../role-based-access-control/overview.md).

Den här artikeln gäller StorSimple 8000-serieenheter som kör Update 3.0 eller senare i Azure-portalen.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-storsimple"></a>RBAC-roller för StorSimple

RBAC kan tilldelas baserat på rollerna. Rollerna se till att vissa behörighetsnivåer baserat på tillgängliga resurser i miljön. Det finns två typer av roller som StorSimple-användare kan välja mellan: inbyggda eller anpassade.

* **Inbyggda roller** -de inbyggda rollerna kan vara ägare, deltagare, läsare eller administratör för användaråtkomst. Mer information finns i [inbyggda roller för Azure rollbaserad åtkomstkontroll](../role-based-access-control/built-in-roles.md).

* **Anpassade roller** – om de inbyggda rollerna inte passar dina behov kan du skapa anpassade RBAC-roller för StorSimple. Om du vill skapa en anpassad RBAC-roll, börja med en inbyggd roll, redigera den och sedan importera den tillbaka i miljön. Ladda ned och överföring av rollen hanteras med hjälp av Azure PowerShell eller Azure CLI. Mer information finns i [skapa anpassade roller för rollbaserad åtkomstkontroll](../role-based-access-control/custom-roles.md).

Om du vill visa de olika rollerna som är tillgängliga för en användare för StorSimple-enhet i Azure-portalen, gå till StorSimple Device Manager-tjänsten och gå sedan till **åtkomstkontroll (IAM) > roller**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Skapa en anpassad roll för StorSimple Infrastrukturadministratör

I följande exempel vi börjar med den inbyggda rollen **läsare** som tillåter användare att visa alla resurs scope men inte att redigera dem eller skapa nya. Vi utökar sedan den här rollen för att skapa en ny anpassad roll infrastruktur för StorSimple-administratören. Den här rollen tilldelas till användare som kan hantera infrastrukturen för StorSimple-enheter.

1. Kör Windows PowerShell som administratör.

2. Logga in på Azure.

    `Connect-AzAccount`

3. Exportera rollen läsare som en JSON-mall på datorn.

    ```powershell
    Get-AzRoleDefinition -Name "Reader"

    Get-AzRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Öppna JSON-filen i Visual Studio. Du ser att en typisk RBAC-roll består av tre huvudområden **åtgärder**, **NotActions**, och **AssignableScopes**.

    I den **åtgärd** avsnittet alla tillåtna åtgärder för den här rollen visas. Varje åtgärd tilldelas från en resursprovider. En StorSimple-infrastruktur-administratör kan använda den `Microsoft.StorSimple` resursprovidern.

    Använd PowerShell för att se samtliga resursprovidrar tillgängliga och registrerade i din prenumeration.

    `Get-AzResourceProvider`

    Du kan också söka efter alla tillgängliga PowerShell-cmdlets för att hantera resursleverantörer.

    I den **NotActions** avsnitt, alla begränsade åtgärder för en viss RBAC-roll visas. I det här exemplet är inga åtgärder begränsade.
    
    Under den **AssignableScopes**, prenumerations-ID: N visas. Kontrollera att RBAC-roll innehåller explicita prenumerations-ID där den används. Om rätt prenumerations-ID har angetts tillåts du inte importera roll i din prenumeration.

    Redigera filen och Tänk på föregående överväganden.

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


Den här rollen bör nu visas i listan över roller i den **åtkomstkontroll** bladet.

![Visa RBAC-roller](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Mer information går du till [anpassade roller](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Exempel på utdata för att skapa en anpassad roll via PowerShell

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

Du beviljar åtkomst inifrån resursen, resursgruppen eller prenumerationen som rolltilldelningen omfattar. När varmed, ärvs, Tänk på åtkomst beviljas på den överordnade noden efter underordnat. Mer information går du till [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md).

1. Gå till **åtkomstkontroll (IAM)**. Klicka på **+ Lägg till** på åtkomstkontroll-bladet.

    ![Lägg till åtkomst till RBAC-roll](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Välj den roll som du vill tilldela, i det här fallet är det den **StorSimple infrastruktur Admin**.

3. Välj den användare, den grupp eller det program i katalogen som du vill bevilja åtkomst till. Du kan söka i katalogen med visningsnamn, e-postadresser och objektidentifierare.

4. Välj **spara** att skapa tilldelningen.

    ![Lägga till behörigheter till RBAC-roll](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

En **lägger till användare** förloppet på meddelandet. När användaren har lagts till, uppdateras listan över användare i Access control.

## <a name="view-permissions-for-the-custom-role"></a>Visa behörigheter för den anpassade rollen

När den här rollen har skapats kan visa du de behörigheter som är associerade med den här rollen i Azure-portalen.

1. Om du vill visa de behörigheter som är associerade med den här rollen går du till **åtkomstkontroll (IAM) > roller > StorSimple infrastruktur Admin**. Lista över användare i den här rollen visas.

2. Välj en administratörsanvändare för StorSimple infrastruktur och klicka på **behörigheter**.

    ![Visa behörigheter för StorSimple Infra administratörsroll](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. De behörigheter som är associerade med den här rollen visas.

    ![Visa användare StorSimple Infra administratörsroll](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [tilldela anpassade roller för interna och externa användare](../role-based-access-control/role-assignments-external-users.md).
