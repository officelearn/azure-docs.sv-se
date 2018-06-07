---
title: Använda rollbaserad åtkomstkontroll för StorSimple | Microsoft Docs
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
ms.openlocfilehash: c500725508d2bf9f09279e665871ab286d9e495a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652077"
---
# <a name="role-based-access-control-for-storsimple"></a>Rollbaserad åtkomstkontroll för StorSimple

Den här artikeln innehåller en kort beskrivning av hur rollbaserad åtkomstkontroll (RBAC) kan användas för din StorSimple-enhet. RBAC ger detaljerad åtkomsthantering för Azure. Använda RBAC och ge bara rätt mängd tillgång till StorSimple användare sköta sitt arbete istället för att ge alla obegränsad åtkomst. Mer information om grunderna för hantering i Azure finns [Kom igång med rollbaserad åtkomstkontroll i Azure portal](../role-based-access-control/overview.md).

Den här artikeln gäller StorSimple 8000-serien enheter som kör uppdatering 3.0 eller senare i Azure-portalen.

## <a name="rbac-roles-for-storsimple"></a>RBAC-roller för StorSimple

RBAC kan tilldelas baserat på rollerna. Rollerna kontrollera vissa behörighetsnivåer baserat på tillgängliga resurser i miljön. Det finns två typer av roller som StorSimple användare kan välja mellan: inbyggt eller anpassat.

* **Inbyggda roller** -inbyggda roller kan vara ägare, deltagare, reader eller administratör för användaråtkomst. Mer information finns i [inbyggda roller för Azure rollbaserad åtkomstkontroll](../role-based-access-control/built-in-roles.md).

* **Anpassade roller** -om inbyggda roller inte uppfyller dina behov, kan du skapa anpassade RBAC-roller för StorSimple. Om du vill skapa en anpassad RBAC-roll, börja med en inbyggd roll, redigera den och sedan importera den igen i miljön. Hämtning och överföra rollen som hanteras med hjälp av Azure PowerShell eller Azure CLI. Mer information finns i [skapa anpassade roller för rollbaserad åtkomstkontroll](../role-based-access-control/custom-roles.md).

Om du vill visa olika roller som är tillgängliga för en användare för StorSimple-enheten i Azure portal, gå till Enhetshanteraren för StorSimple-tjänsten och gå sedan till **åtkomstkontroll (IAM) > roller**.


## <a name="create-a-custom-role-for-storsimple-infrastructure-administrator"></a>Skapa en anpassad roll för StorSimple Infrastrukturadministratör

I följande exempel vi börjar med den inbyggda rollen **Reader** som gör det möjligt för användare att visa alla resurs-scope, men inte redigera dem eller skapa nya. Vi utökar den här rollen för att skapa en ny anpassad roll administratören StorSimple-infrastrukturen. Den här rollen tilldelas användare som kan hantera infrastrukturen för StorSimple-enheter.

1. Kör Windows PowerShell som administratör.

2. Logga in på Azure.

    `Connect-AzureRmAccount`

3. Exportera rollen läsare som en JSON-mall på datorn.

    ```
    Get-AzureRMRoleDefinition -Name "Reader"

    Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json
    ```

4. Öppna JSON-filen i Visual Studio. Du ser att en typisk RBAC-rollen består av tre huvudavsnitt **åtgärder**, **NotActions**, och **AssignableScopes**.

    I den **åtgärd** avsnittet alla tillåtna åtgärder för den här rollen visas. Varje åtgärd tilldelas från en resursleverantör. En StorSimple-infrastrukturen administratör använder den `Microsoft.StorSimple` resursprovidern.

    Använd PowerShell för att se alla resursleverantörer tillgänglig och registrerade i din prenumeration.

    `Get-AzureRMResourceProvider`

    Du kan också söka efter alla tillgängliga PowerShell-cmdletar för hantering av resursleverantörer.

    I den **NotActions** avsnitten alla begränsade åtgärderna för en viss roll RBAC visas. I det här exemplet är inga åtgärder begränsade.
    
    Under den **AssignableScopes**, prenumerations-ID: N visas. Kontrollera att rollen RBAC innehåller explicit prenumerations-ID där den används. Om rätt prenumerations-ID har angetts är inte tillåtna att importera rollen i din prenumeration.

    Redigera filen tänka i åtanke på föregående.

    ```
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

6. Importera den anpassade rollen som RBAC tillbaka till miljön.

    `New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"`


Den här rollen ska nu visas i listan över roller i den **åtkomstkontroll** bladet.

![Visa RBAC-roller](./media/storsimple-8000-role-based-access-control/rbac-role-types.png)

Mer information finns på [anpassade roller](../role-based-access-control/custom-roles.md).

### <a name="sample-output-for-custom-role-creation-via-the-powershell"></a>Exempel på utdata för att skapa en anpassad roll via PowerShell

```
PS C:\WINDOWS\system32> Connect-AzureRmAccount

Environment           : AzureCloud
Account               : john.doe@contoso.com
TenantId              : <tenant_ID>
SubscriptionId        : <subscription_ID>
SubscriptionName      : Internal Consumption
CurrentStorageAccount :

PS C:\WINDOWS\system32> Get-AzureRMRoleDefinition -Name "Reader"

Name             : Reader
Id               : <guid>
IsCustom         : False
Description      : Lets you view everything, but not make any changes.
Actions          : {*/read}
NotActions       : {}
AssignableScopes : {/}

PS C:\WINDOWS\system32> Get-AzureRMRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\ssrbaccustom.json

PS C:\WINDOWS\system32> New-AzureRMRoleDefinition -InputFile "C:\ssrbaccustom.json"

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

PS C:\WINDOWS\system32>
```

## <a name="add-users-to-the-custom-role"></a>Lägga till användare i den anpassade rollen

Du beviljar åtkomst inifrån resursen, resursgruppen eller prenumerationen som rolltilldelningen omfattar. När åtkomst, Tänk att åtkomst till den överordnade noden ärvs av underordnade. Mer information finns på [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md).

1. Gå till **åtkomstkontroll (IAM)**. Klicka på **+ Lägg till** i bladet Access control.

    ![Lägg till åtkomst till RBAC roll](./media/storsimple-8000-role-based-access-control/rbac-add-role.png)

2. Välj den roll som du vill tilldela, i det här fallet är det den **StorSimple-infrastrukturen Admin**.

3. Välj den användare, den grupp eller det program i katalogen som du vill bevilja åtkomst till. Du kan söka i katalogen med visningsnamn, e-postadresser och objektidentifierare.

4. Välj **spara** skapa tilldelningen.

    ![Lägg till behörigheter RBAC roll](./media/storsimple-8000-role-based-access-control/rbac-create-role-infra-admin.png)

En **lägger till användare** meddelanden spårar förloppet. När användaren har lagts till har i listan över användare i åtkomstkontroll uppdaterats.

## <a name="view-permissions-for-the-custom-role"></a>Visa behörigheter för den anpassade rollen

När den här rollen har skapats kan visa du de behörigheter som är associerade med den här rollen i Azure-portalen.

1. Om du vill visa de behörigheter som är associerade med den här rollen, gå till **åtkomstkontroll (IAM) > roller > StorSimple-infrastrukturen Admin**. En lista över användare i den här rollen visas.

2. Välj en administratörsanvändare StorSimple-infrastrukturen och klicka på **behörigheter**.

    ![Visa behörigheter för rollen StorSimple infrastruktur administratör](./media/storsimple-8000-role-based-access-control/rbac-roles-view-permissions.png)

3. Behörigheter som associeras med den här rollen visas.

    ![Visa användare i rollen Administratör infrastruktur StorSimple](./media/storsimple-8000-role-based-access-control/rbac-infra-admin-permissions1.png)


## <a name="next-steps"></a>Nästa steg

Lär dig hur du [tilldela anpassade roller för interna och externa användare](../role-based-access-control/role-assignments-external-users.md).

