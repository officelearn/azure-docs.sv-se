---
title: Fastställa orsaker till icke-kompatibilitet
description: När en resurs är icke-kompatibla, finns det många möjliga orsaker. Lär dig att ta reda på vad som orsakade överträdelsen.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 8a593e92d7f24885c35043b874528e881d2e021e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59276107"
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

## <a name="change-history-preview"></a>Ändra historik (förhandsversion)

Som en del av en ny **förhandsversion**, de senaste 14 dagarna av ändring historik är tillgänglig för alla Azure-resurser som stöder [slutföra läge borttagning](../../../azure-resource-manager/complete-mode-deletion.md). Ändra historik innehåller information om när en ändring har identifierats och en _visual diff_ för varje ändring. Identifiering av ändring av utlöses när Resource Manager-egenskaper läggs till, tas bort eller ändras.

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

1. På den **översikt** eller **efterlevnad** väljer du en princip i någon **kompatibilitetstillstånd**.

1. Under den **resurskompatibilitet** fliken den **principefterlevnad** väljer du en resurs.

1. Välj den **ändra historik (förhandsversion)** fliken på den **Resurskompatibilitet** sidan. En lista över identifierat ändringar, om några finns, visas.

   ![Fliken Ändra historik på Resurskompatibilitet sida](../media/determine-non-compliance/change-history-tab.png)

1. Välj en av de identifierade ändringarna. Den _visual diff_ för resursen som visas på den **ändringshistorik** sidan.

   ![Principen ändra historik Visual Diff på sidan för ändra historik](../media/determine-non-compliance/change-history-visual-diff.png)

Den _visual diff_ aides identifiera ändringar till en resurs. Ändringar som har identifierats kan inte vara relaterade till det aktuella tillståndet för efterlevnad för resursen.

## <a name="next-steps"></a>Nästa steg

- Se exempel på [Azure Policy-exempel](../samples/index.md)
- Granska den [Policy-definitionsstruktur](../concepts/definition-structure.md)
- Granska [förstå effekterna av princip](../concepts/effects.md)
- Förstå hur du [skapa principer programmässigt](programmatically-create.md)
- Lär dig hur du [hämta data för kompatibilitetsinställningar](getting-compliance-data.md)
- Lär dig hur du [åtgärda icke-kompatibla resurser](remediate-resources.md)
- Se över vad en hanteringsgrupp är med sidan om att [organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md)