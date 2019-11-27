---
title: Förstå resurs låsning
description: Lär dig mer om låsnings alternativen i Azure-ritningar för att skydda resurser när du tilldelar en skiss.
ms.date: 04/24/2019
ms.topic: conceptual
ms.openlocfilehash: 50f506cc57f67ca2ae2b07e342750d6c5099e739
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406411"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Förstå resurs låsning i Azure-ritningar

Att skapa konsekventa miljöer i skala är bara riktigt värdefullt om det finns en mekanism för att upprätthålla denna konsekvens. Den här artikeln förklarar hur resurs låsning fungerar i Azure-ritningar. Om du vill se ett exempel på resurs låsning och tillämpning av _neka-tilldelningar_, se själv studie kursen [skydda nya resurser](../tutorials/protect-new-resources.md) .

## <a name="locking-modes-and-states"></a>Lås lägen och tillstånd

Låsnings läget gäller för skiss tilldelningen och har tre alternativ: **Lås inte**, **skrivskyddad**eller **Ta inte bort**. Lås läget konfigureras under en artefakt distribution under en skiss tilldelning. Du kan ställa in ett annat lås läge genom att uppdatera skiss tilldelningen.
Lås lägen kan dock inte ändras utanför ritningar.

Resurser som har skapats av artefakter i en skiss tilldelning har fyra tillstånd: **inte låst**, **skrivskyddad**, **kan inte redigera/ta bort**eller **kan inte tas bort**. Varje artefakt typ kan vara i **låst** läge. Följande tabell kan användas för att fastställa en resurs status:

|Läge|Artefakt resurs typ|Status|Beskrivning|
|-|-|-|-|
|Lås inte|*|Inte låst|Resurser skyddas inte av ritningar. Det här läget används också för resurser som läggs till i en **skrivskyddad** eller **inte tar bort** artefakten för resurs gruppen utanför en skiss tilldelning.|
|Skrivskyddad|Resursgrupp|Det går inte att redigera/ta bort|Resurs gruppen är skrivskyddad och taggarna i resurs gruppen kan inte ändras. Det går inte att lägga till, flytta, ändra eller ta bort resurser som **inte är låsta** från den här resurs gruppen.|
|Skrivskyddad|Icke-resurs grupp|Skrivskyddad|Resursen kan inte ändras på något sätt: inga ändringar och det går inte att ta bort den.|
|Ta inte bort|*|Kan inte ta bort|Resurserna kan ändras, men de kan inte tas bort. Det går inte att lägga till, flytta, ändra eller ta bort resurser som **inte är låsta** från den här resurs gruppen.|

## <a name="overriding-locking-states"></a>Åsidosätter lås tillstånd

Det är vanligt vis möjligt för någon med lämplig [rollbaserad åtkomst kontroll](../../../role-based-access-control/overview.md) (RBAC) för prenumerationen, t. ex. ägar rollen, att tillåtas att ändra eller ta bort resurser. Den här åtkomsten är inte fallet när ritningar använder lås som en del av en distribuerad tilldelning. Om tilldelningen har angetts med alternativet **skrivskyddad** eller **Ta inte bort** , inte ens prenumerations ägaren kan utföra den blockerade åtgärden på den skyddade resursen.

Detta säkerhets mått skyddar konsekvensen för den definierade skissen och miljön som den har utformats för att skapa från oavsiktlig eller program mässig borttagning eller ändring.

## <a name="removing-locking-states"></a>Tar bort lås tillstånd

Om det blir nödvändigt att ändra eller ta bort en resurs som skyddas av en tilldelning, finns det två sätt att göra det.

- Uppdatera skiss tilldelningen till låsnings läge med **Lås inte**
- Ta bort skiss tilldelningen

När tilldelningen tas bort tas låsen som skapats av ritningar bort. Resursen är dock kvar bakom och måste tas bort på vanligt sätt.

## <a name="how-blueprint-locks-work"></a>Hur skissen låser sig

Åtgärden för [att neka en RBAC-](../../../role-based-access-control/deny-assignments.md) åtgärd tillämpas på artefakt resurser under tilldelningen av en skiss om tilldelningen har valt alternativet **skrivskyddad** eller **Ta inte bort** . Neka-åtgärden läggs till av den hanterade identiteten för skiss tilldelningen och kan bara tas bort från artefakt resurserna av samma hanterade identitet. Det här säkerhets måttet tillämpar låsnings funktionen och förhindrar borttagning av skiss låset utanför ritningar.

![Skissen neka tilldelning för resurs gruppen](../media/resource-locking/blueprint-deny-assignment.png)

[Egenskaperna för neka tilldelning](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) för varje läge är följande:

|Läge |Behörigheter. åtgärder |Permissions.NotActions |Principals[i].Type |ExcludePrincipals[i].Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Skrivskyddad |**\*** |**\*/Read** |SystemDefined (alla) |skiss tilldelning och användardefinierad i **excludedPrincipals** |Resurs grupp- _Sant_; Resurs- _falskt_ |
|Ta inte bort |**\*/Delete** | |SystemDefined (alla) |skiss tilldelning och användardefinierad i **excludedPrincipals** |Resurs grupp- _Sant_; Resurs- _falskt_ |

> [!IMPORTANT]
> Azure Resource Manager cachelagrar roll tilldelnings information i upp till 30 minuter. Till följd av detta kan neka-tilldelningar neka åtgärder på skiss resurser inte omedelbart tillämpas fullständigt. Under den här tids perioden kan det vara möjligt att ta bort en resurs som är avsedd att skyddas av skiss lås.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Undanta ett huvud konto från en neka-tilldelning

I vissa design-eller säkerhets scenarier kan det vara nödvändigt att undanta ett huvud konto från den [neka-tilldelning](../../../role-based-access-control/deny-assignments.md) som skiss tilldelningen skapar. Detta görs i REST API genom att lägga till upp till fem värden i **excludedPrincipals** -matrisen i **Lås** -egenskapen när [tilldelningen skapas](/rest/api/blueprints/assignments/createorupdate). Detta är ett exempel på en begär ande text som innehåller **excludedPrincipals**:

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="next-steps"></a>Nästa steg

- Följ själv studie kursen [skydda nya resurser](../tutorials/protect-new-resources.md) .
- Mer information om [livscykeln för en skiss](lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](sequencing-order.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).