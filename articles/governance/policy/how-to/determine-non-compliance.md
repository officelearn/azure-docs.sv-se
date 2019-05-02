---
title: Fastställa orsaker till icke-kompatibilitet
description: När en resurs är icke-kompatibla, finns det många möjliga orsaker. Lär dig att ta reda på vad som orsakade överträdelsen.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 2f856e9c42b26d4e286493e2eb5d019a8cff6c23
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868724"
---
# <a name="determine-causes-of-non-compliance"></a>Fastställa orsaker till icke-kompatibilitet

När en Azure-resurs bedöms vara icke-kompatibla till en regel, är det bra att förstå vilken del av regeln resursen inte är kompatibel med. Det är också användbart för att förstå vilken ändring ändras en tidigare kompatibel resurs för att göra det icke-kompatibla. Det finns två sätt att hitta den här informationen:

> [!div class="checklist"]
> - [Information om kompatibilitet](#compliance-details)
> - [Ändra historik (förhandsversion)](#change-history-preview)

## <a name="compliance-details"></a>Information om efterlevnad

När en resurs är icke-kompatibla, information om kompatibilitet för den resursen är tillgängliga från den **principefterlevnad** sidan. Informationsfönstret efterlevnad innehåller följande information:

- Information om resursen, till exempel namn, typ, plats och resurs-ID
- Kompatibilitetsstatus och tidsstämpel för senaste utvärdering för den aktuella principtilldelningen för
- En lista över _orsaker_ för resursen icke-kompatibilitet

> [!IMPORTANT]
> Som information om kompatibilitet för en _icke-kompatibla_ resurs visar det aktuella värdet för egenskaper på den aktuella, måste användaren ha **läsa** åtgärd för att den **typ** av resursen. Till exempel om den _icke-kompatibla_ resursen är **Microsoft.Compute/virtualMachines** användaren måste ha den **Microsoft.Compute/virtualMachines/read** åtgärden. Om användaren inte har den nödvändiga åtgärden, visas ett åtkomstfel.

Följ dessa steg om du vill visa information om kompatibilitet:

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

1. På den **översikt** eller **efterlevnad** väljer du en princip i en **kompatibilitetstillstånd** dvs _icke-kompatibla_.

1. Under den **resurskompatibilitet** fliken den **principefterlevnad** sidan, högerklicka på eller välj ellipsen för en resurs i en **kompatibilitetstillstånd** dvs  _Icke-kompatibla_. Välj sedan **visa information om kompatibilitet**.

   ![Information om alternativet efterlevnad](../media/determine-non-compliance/view-compliance-details.png)

1. Den **efterlevnadsinformation** fönstret visar information från senaste utvärderingen av resursen till den aktuella principtilldelningen för. I det här exemplet fältet **Microsoft.Sql/servers/version** identifieras som _12.0_ medan principdefinitionen förväntat _14,0_. Om resursen är icke-kompatibla av flera skäl, finns var och en i det här fönstret.

   ![Informationsfönstret för efterlevnad och orsakerna till inkompatibilitet](../media/determine-non-compliance/compliance-details-pane.png)

   För en **auditIfNotExists** eller **deployIfNotExists** principdefinition informationen innehåller den **details.type** egenskapen och eventuella valfria egenskaper. En lista i [auditIfNotExists egenskaper](../concepts/effects.md#auditifnotexists-properties) och [deployIfNotExists egenskaper](../concepts/effects.md#deployifnotexists-properties). **Senast utvärderad resource** är en relaterad resurs från den **information** avsnitt i definitionen.

   Exempel partiella **deployIfNotExists** definition:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![Informationsfönstret för efterlevnad – * ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Att skydda data, när ett egenskapsvärde är en _hemlighet_ det aktuella värdet visar asterisker.

Dessa uppgifter förklara varför en resurs för närvarande inte är kompatibel, men visar inte när ändringen har gjorts till den resurs som gör att det har blivit inkompatibla. Den informationen finns i [ändringshistorik (förhandsversion)](#change-history-preview) nedan.

### <a name="compliance-reasons"></a>Efterlevnadsskäl

Matrisen följande mappar varje möjligt _orsak_ till den ansvariga [villkor](../concepts/definition-structure.md#conditions) i principdefinitionen:

|Orsak | Tillstånd |
|-|-|
|Aktuellt värde måste innehålla målvärdet som en nyckel. |containsKey eller **inte** notContainsKey |
|Aktuellt värde måste innehålla målvärdet. |innehåller eller **inte** notContains |
|Aktuellt värde måste vara lika med målvärdet. |är lika med eller **inte** notEquals |
|Aktuellt värde måste finnas. |Det finns |
|Aktuellt värde måste finnas i målvärdet. |i eller **inte** notIn |
|Aktuellt värde måste vara som målvärdet. |som eller **inte** notLike |
|Det aktuella värdet måste matcha målvärdet skiftlägeskänsligt. |matcha eller **inte** notMatch |
|Det aktuella värdet måste matcha målvärdet skiftlägesokänsligt. |matchInsensitively eller **inte** notMatchInsensitively |
|Aktuellt värde får inte innehålla målvärdet som en nyckel. |notContainsKey eller **inte** containsKey|
|Aktuellt värde får inte innehålla målvärdet. |notContains eller **inte** innehåller |
|Aktuellt värde får inte vara lika med målvärdet. |notEquals eller **inte** är lika med |
|Aktuellt värde får inte finnas. |**inte** finns  |
|Aktuellt värde får inte finnas i målvärdet. |notIn eller **inte** i |
|Aktuellt värde får inte vara som målvärdet. |notLike eller **inte** som |
|Det aktuella värdet får inte matcha målvärdet skiftlägeskänsligt. |notMatch eller **inte** matchar |
|Det aktuella värdet får inte matcha målvärdet skiftlägesokänsligt. |notMatchInsensitively eller **inte** matchInsensitively |
|Inga relaterade resurser matchar effektinformationen i principdefinitionen. |En resurs av typen som definierats i **then.details.type** och relaterad till den resurs som definierats i den **om** delen av principregeln finns inte. |

## <a name="compliance-details-for-guest-configuration"></a>Information om kompatibilitet för gäst-konfiguration

För _granska_ principer i den _gäst Configuration_ kategori, det kan finnas flera inställningar som utvärderas i den virtuella datorn och du behöver visa information per inställning. Till exempel om du granskning för en lista över installerade program och tilldelningsstatus är _icke-kompatibla_, måste du veta vilka specifika program som saknas.

Du kan inte heller åtkomst att logga in på den virtuella datorn direkt men du måste rapportera om varför den virtuella datorn är _icke-kompatibla_. Du kan till exempel granska att virtuella datorer är anslutna till rätt domän och inkluderar aktuella domänmedlemskap i information för rapportering.

### <a name="azure-portal"></a>Azure Portal

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

1. På den **översikt** eller **efterlevnad** väljer du en principtilldelning för alla som innehåller en definition av principen för gäst-konfiguration är _icke-kompatibla_.

1. Välj en _granska_ principen i initiativet är _icke-kompatibla_.

   ![Visa audit definition information](../media/determine-non-compliance/guestconfig-audit-compliance.png)

1. På den **resurskompatibilitet** fliken tillhandahålls följande information:

   - **Namn på** -namnet på Konfigurationstilldelningar gäst.
   - **Överordnad resurs** -den virtuella datorn i en _icke-kompatibla_ tillstånd för den valda konfigurationen för gäst-tilldelningen.
   - **Resurstypen** – _guestConfigurationAssignments_ fullständigt namn.
   - **Senast utvärderad** – den senaste gången som gäst konfigurationstjänsten ett meddelande om Azure Policy om tillståndet för den virtuella måldatorn.

   ![Visa efterlevnadsinformation.](../media/determine-non-compliance/guestconfig-assignment-view.png)

1. Välj gäst Configuration tilldelningsnamn i den **namn** kolumnen för att öppna den **Resurskompatibilitet** sidan.

1. Välj den **visa resurs** längst upp på sidan för att öppna den **gäst tilldelning** sidan.

Den **gäst tilldelning** sidan visar information om alla tillgängliga kompatibilitet. Varje rad i vyn representerar en bedömning som utfördes på den virtuella datorn. I den **orsak** kolumn, en fras som beskriver varför den gäst-tilldelningen är _icke-kompatibla_ visas. Om du granskning att virtuella datorer bör vara ansluten till en domän, till exempel den **orsak** kolumnen visar text, inklusive det aktuella medlemskapet i domänen.

![Visa efterlevnadsinformation.](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Du kan också visa information om kompatibilitet från Azure PowerShell. Kontrollera först att du har installerat modulen gäst-konfiguration.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Du kan visa den aktuella statusen för alla gäst-tilldelningar för en virtuell dator med följande kommando:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Visa endast de _orsak_ fras som beskriver varför den virtuella datorn är _icke-kompatibla_, returnerar endast egenskapen orsak som underordnade.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Du kan också skapa en kompatibilitetshistorik för gäst-tilldelningar inom omfånget för den virtuella datorn. Utdata från det här kommandot finns information om varje rapport för den virtuella datorn.

> [!NOTE]
> Utdata kan returnera stora mängder data. Vi rekommenderar att du lagrar utdata i en variabel.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

För att förenkla den här vyn kan du använda den **ShowChanged** parametern. Utdata från det här kommandot innehåller bara de rapporter som följt av en ändring i efterlevnadsstatus.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>Ändra historik (förhandsversion)

Som en del av en ny **förhandsversion**, de senaste 14 dagarna ändringshistoria är tillgängliga för alla Azure-resurser som stöder [slutföra läge borttagning](../../../azure-resource-manager/complete-mode-deletion.md). Ändra historik innehåller information om när en ändring har identifierats och en _visual diff_ för varje ändring. Identifiering av ändring av utlöses när Resource Manager-egenskaper läggs till, tas bort eller ändras.

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

1. På den **översikt** eller **efterlevnad** väljer du en princip i någon **kompatibilitetstillstånd**.

1. Under den **resurskompatibilitet** fliken den **principefterlevnad** väljer du en resurs.

1. Välj den **ändra historik (förhandsversion)** fliken på den **Resurskompatibilitet** sidan. En lista över identifierat ändringar, om några finns, visas.

   ![Fliken Ändra historik på Resurskompatibilitet sida](../media/determine-non-compliance/change-history-tab.png)

1. Välj en av de identifierade ändringarna. Den _visual diff_ för resursen som visas på den **ändringshistorik** sidan.

   ![Principen ändra historik Visual Diff på sidan för ändra historik](../media/determine-non-compliance/change-history-visual-diff.png)

Den _visual diff_ aides identifiera ändringar till en resurs. Ändringar som har identifierats kan inte vara relaterade till det aktuella tillståndet för efterlevnad för resursen.

Ändra historikdata tillhandahålls av [Azure Resource Graph](../../resource-graph/overview.md). Om du vill fråga efter den här informationen utanför Azure-portalen, se [hämta resursändringar](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Nästa steg

- Se exempel på [Azure Policy-exempel](../samples/index.md).
- Granska [Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Förstå hur du [skapa principer programmässigt](programmatically-create.md).
- Lär dig hur du [hämta kompatibilitetsdata](getting-compliance-data.md).
- Lär dig hur du [åtgärda icke-kompatibla resurser](remediate-resources.md).
- Granska vilka en hanteringsgrupp är med [organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).