---
title: Fastställa orsaker till icke-kompatibilitet
description: När en resurs inte är kompatibel finns det många möjliga orsaker. Lär dig att ta reda på vad som orsakade bristande efterlevnad.
ms.date: 04/26/2019
ms.topic: how-to
ms.openlocfilehash: c931831ddf3cc727b9861e75969eac3bf00c9e45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264640"
---
# <a name="determine-causes-of-non-compliance"></a>Fastställa orsaker till icke-kompatibilitet

När en Azure-resurs bedöms vara icke-kompatibel med en principregel är det bra att förstå vilken del av regeln som resursen inte är kompatibel med. Det är också användbart att förstå vilken ändring som ändrade en tidigare kompatibel resurs för att göra den inte kompatibel. Det finns två sätt att hitta den här informationen:

> [!div class="checklist"]
> - [Information om efterlevnad](#compliance-details)
> - [Ändra historik (förhandsgranskning)](#change-history)

## <a name="compliance-details"></a>Information om efterlevnad

När en resurs inte är kompatibel är efterlevnadsinformationen för den resursen tillgänglig från sidan **Principefterlevnad.** Fönstret information om efterlevnad innehåller följande information:

- Resursinformation som namn, typ, plats och resurs-ID
- Efterlevnadstillstånd och tidsstämpel för den senaste utvärderingen för den aktuella principtilldelningen
- En lista över _orsaker till_ att resursen inte uppfyller kraven

> [!IMPORTANT]
> Eftersom efterlevnadsinformationen för en _icke-kompatibel_ resurs visar det aktuella värdet för egenskaper på den resursen, måste användaren ha **läst** åtgärd till **resurstypen.** Om den _icke-kompatibla_ resursen till exempel är **Microsoft.Compute/virtualMachines** måste användaren ha åtgärden **Microsoft.Compute/virtualMachines/read.** Om användaren inte har den nödvändiga åtgärden visas ett åtkomstfel.

Så här visar du efterlevnadsinformationen:

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

1. På sidan **Översikt** eller **Efterlevnad** väljer du en princip i ett **efterlevnadstillstånd** som inte är _kompatibelt_.

1. Högerklicka eller välj ellipsen för en resurs under fliken **Resursefterlevnad** på _Non-compliant_sidan **Principefterlevnad.** **compliance state** Välj sedan **Visa efterlevnadsinformation**.

   ![Visa alternativet För efterlevnadsinformation](../media/determine-non-compliance/view-compliance-details.png)

1. I fönstret **Information om efterlevnad** visas information från den senaste utvärderingen av resursen till den aktuella principtilldelningen. I det här exemplet visar sig fältet **Microsoft.Sql/servers/version** vara _12.0_ medan principdefinitionen förväntades _14.0_. Om resursen inte är kompatibel av flera skäl visas var och en i det här fönstret.

   ![Fönstret Information om efterlevnad och orsaker till bristande efterlevnad](../media/determine-non-compliance/compliance-details-pane.png)

   För en **auditIfNotExists** eller **deployIfNotExists** principdefinition innehåller informationen egenskapen **details.type** och eventuella valfria egenskaper. En lista finns i [egenskaper för auditIfNotExists](../concepts/effects.md#auditifnotexists-properties) och [deployIfNotExists egenskaper](../concepts/effects.md#deployifnotexists-properties). **Senast utvärderad resurs** är en relaterad resurs från **informationsavsnittet** i definitionen.

   Exempel partiell **deployIfNotExists** definition:

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

   ![Fönstret Information om efterlevnad - *ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> För att skydda data, när ett egenskapsvärde är en _hemlighet,_ visar det aktuella värdet asterisker.

De här detaljerna förklarar varför en resurs för närvarande inte är kompatibel, men visas inte när ändringen gjordes av resursen som gjorde att den inte var kompatibel. Den informationen finns i [Ändra historik (förhandsgranskning)](#change-history) nedan.

### <a name="compliance-reasons"></a>Orsaker till efterlevnad

Matrisen kartlägger efter varje möjlighet _resonerar_ till [ansvarigt villkorar](../concepts/definition-structure.md#conditions) i den politiska definitionen:

|Orsak | Villkor |
|-|-|
|Det aktuella värdet måste innehålla målvärdet som en nyckel. |innehållerNyckel eller **not** inteContainsKey |
|Aktuellt värde måste innehålla målvärdet. |innehåller eller **inte** inteContains |
|Det aktuella värdet måste vara lika med målvärdet. |är lika med eller **inte** InteEquals |
|Det aktuella värdet måste vara mindre än målvärdet. |mindre eller **inte** störreOrEquals |
|Det aktuella värdet måste vara större än eller lika med målvärdet. |störreOrEquals eller **inte** mindre |
|Det aktuella värdet måste vara större än målvärdet. |större eller **inte** utReorEquals |
|Det aktuella värdet måste vara mindre än eller lika med målvärdet. |mindre Än Minst Eller **inte** större |
|Det aktuella värdet måste finnas. |Finns |
|Det aktuella värdet måste finnas i målvärdet. |i eller **inte** InteIn |
|Det aktuella värdet måste vara som målvärdet. |gilla eller **inte** InteSom |
|Aktuellt värde måste skiftlägeskänsligt matcha målvärdet. |matcha eller **inteMatcha** |
|Det aktuella värdet måste skiftlägesokänsligt matcha målvärdet. |matchInkänsligt eller **not** inteMatchInsensitively |
|Det aktuella värdet får inte innehålla målvärdet som en nyckel. |notContainsKey eller **inte** innehållerNyckel|
|Det aktuella värdet får inte innehålla målvärdet. |inte Innehåller eller **inte** innehåller |
|Det aktuella värdet får inte vara lika med målvärdet. |notEquals eller **inte** är lika med |
|Det aktuella värdet får inte finnas. |**inte** existerar  |
|Det aktuella värdet får inte finnas i målvärdet. |notIn eller **inte** i |
|Det aktuella värdet får inte vara som målvärdet. |notLike eller **inte** som |
|Det aktuella värdet får inte skiftlägeskänsligt matcha målvärdet. |notMatch eller **inte** matcha |
|Det aktuella värdet får inte skiftlägesokänsligt matcha målvärdet. |inteMatchInsensitively eller **inte** matchInsensitively |
|Inga relaterade resurser matchar effektdetaljerna i principdefinitionen. |En resurs av typen som definieras i **then.details.type** och relaterad till resursen som definierats i **om** delen av principregeln inte finns. |

## <a name="compliance-details-for-guest-configuration"></a>Efterlevnadsinformation för gästkonfiguration

För _auditIfNotExists_ principer i kategorin _Gästkonfiguration_ kan det finnas flera inställningar utvärderade inuti den virtuella datorn och du måste visa information per inställning. Om du till exempel granskar en lista över lösenordsprinciper och bara en av dem har status _som icke-kompatibla_måste du veta vilka specifika lösenordsprinciper som inte uppfyller kraven och varför.

Du kanske inte heller har åtkomst till inloggning till den virtuella datorn direkt, men du måste rapportera om varför den virtuella datorn inte är _kompatibel_.

### <a name="azure-portal"></a>Azure Portal

Börja med att följa samma steg i avsnittet ovan för att visa information om policyefterlevnad.

Klicka på länken **Senast utvärderad resurs**i fönstret **Kompatibilitetsinformation** .

   ![Visa definitionsinformation för auditIfNotExists](../media/determine-non-compliance/guestconfig-auditifnotexists-compliance.png)

På sidan **Gästtilldelning** visas alla tillgängliga efterlevnadsuppgifter. Varje rad i vyn representerar en utvärdering som utfördes inuti maskinen. I kolumnen **Orsak** visas en fras som beskriver varför gästtilldelningen inte är _kompatibel._ Om du till exempel granskar lösenordsprinciper visar kolumnen **Orsak** text inklusive det aktuella värdet för varje inställning.

![Visa efterlevnadsinformation](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

Du kan också visa efterlevnadsinformation från Azure PowerShell. Kontrollera först att du har modulen Gästkonfiguration installerad.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Du kan visa aktuell status för alla gästtilldelningar för en virtuell dator med följande kommando:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Om du bara vill visa orsaksfrasen som beskriver varför den virtuella datorn inte är _kompatibel_returnerar du bara egenskapen Reason child. _reason_

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

Du kan också ange en efterlevnadshistorik för gästtilldelningar i omfånget för datorn. Utdata från det här kommandot innehåller information om varje rapport för den virtuella datorn.

> [!NOTE]
> Utdata kan returnera en stor mängd data. Vi rekommenderar att du lagrar utdata i en variabel.

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

Om du vill förenkla den här vyn använder du parametern **ShowChanged.** Utdata från det här kommandot innehåller endast rapporter som följde på en ändring av efterlevnadsstatus.

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

## <a name="change-history-preview"></a><a name="change-history"/>Ändra historik (förhandsgranskning)

Som en del av en ny **offentlig förhandsversion**är de senaste 14 dagarnas ändringshistorik tillgänglig för alla Azure-resurser som stöder [fullständig borttagning av läge](../../../azure-resource-manager/templates/complete-mode-deletion.md). Ändringshistoriken innehåller information om när en ändring upptäcktes och en _visuell diff_ för varje ändring. En ändringsidentifiering utlöses när Resource Manager-egenskaperna läggs till, tas bort eller ändras.

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

1. På sidan **Översikt** eller **Efterlevnad** väljer du en princip i alla **efterlevnadstillstånd**.

1. Välj en resurs under fliken **Resursefterlevnad** på sidan **Principefterlevnad.**

1. Välj fliken **Ändra historik (förhandsgranskning)** på sidan **Resursefterlevnad.** En lista över identifierade ändringar, om sådana finns, visas.

   ![Fliken Historik för Azure-principändring på sidan Resursefterlevnad](../media/determine-non-compliance/change-history-tab.png)

1. Markera en av de identifierade ändringarna. _Resursens visuella diff_ för resursen visas på sidan **Ändra historik.**

   ![Visual Diff för visuella datahistorik för Azure Policy Change på sidan Ändra historik](../media/determine-non-compliance/change-history-visual-diff.png)

Den _visuella diff_ medhjälpare att identifiera ändringar i en resurs. De ändringar som har identifierats kanske inte är relaterade till resursens aktuella efterlevnadstillstånd.

Ändringshistorikdata tillhandahålls av [Azure Resource Graph](../../resource-graph/overview.md). Information om hur du frågar den här informationen utanför Azure-portalen finns i [Hämta resursändringar](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Nästa steg

- Granska exempel på [Azure Policy-exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Förstå hur du [programmässigt skapar principer](programmatically-create.md).
- Läs om hur du [hämtar efterlevnadsdata](get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).
