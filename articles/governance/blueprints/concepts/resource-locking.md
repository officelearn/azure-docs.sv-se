---
title: Förstå resurslåsning
description: Lär dig mer om låsningsalternativen i Azure Blueprints för att skydda resurser när du tilldelar en skiss.
ms.date: 03/25/2020
ms.topic: conceptual
ms.openlocfilehash: 94ed8efd0d6c654cba129dfc69fbfe5add7a0824
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383595"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Förstå resurslåsning i Azure Blueprints

Skapandet av konsekventa miljöer i stor skala är bara verkligt värdefullt om det finns en mekanism för att upprätthålla denna konsekvens. I den här artikeln beskrivs hur resurslåsning fungerar i Azure Blueprints. Om du vill se ett exempel på resurslåsning och tillämpning av _neka tilldelningar_läser du självstudien [för att skydda nya resurser.](../tutorials/protect-new-resources.md)

> [!NOTE]
> Resurslås som distribueras av Azure Blueprints tillämpas endast på resurser som distribueras av skisstilldelningen. Befintliga resurser, till exempel de i resursgrupper som redan finns, har inte lås tillagda i dem.

## <a name="locking-modes-and-states"></a>Låsningslägen och låsningslägen

Låsläge gäller för skisstilldelningen och har tre alternativ: **Lås inte**, **Skrivskyddat**eller **Ta inte bort**. Låsläget konfigureras under artefaktdistributionen under en skisstilldelning. Ett annat låsläge kan ställas in genom att uppdatera skisstilldelningen.
Låslägen kan dock inte ändras utanför Azure Blueprints.

Resurser som skapas av artefakter i en skisstilldelning har fyra lägen: **Inte låst**, **Skrivskyddad**, **Kan inte redigera/ta bort**eller Kan inte ta **bort**. Varje artefakttyp kan vara i läget **Inte låst.** Följande tabell kan användas för att bestämma tillståndet för en resurs:

|Läge|Artefaktresurstyp|Status|Beskrivning|
|-|-|-|-|
|Lås inte|*|Inte låst|Resurser skyddas inte av Azure Blueprints. Det här tillståndet används också för resurser som läggs till i en **skrivskyddad** eller Ta bort resursgruppsartefakt från utanför en skisstilldelning. **Do Not Delete**|
|Skrivskydd|Resursgrupp|Det går inte att redigera/ta bort|Resursgruppen är skrivskyddad och taggar i resursgruppen kan inte ändras. **Låsta** resurser kan inte läggas till, flyttas, ändras eller tas bort från den här resursgruppen.|
|Skrivskydd|Grupp som inte är resurs än resurs|Skrivskydd|Resursen kan inte ändras på något sätt – inga ändringar och den kan inte tas bort.|
|Ta inte bort|*|Det går inte att ta bort|Resurserna kan ändras, men kan inte tas bort. **Låsta** resurser kan inte läggas till, flyttas, ändras eller tas bort från den här resursgruppen.|

## <a name="overriding-locking-states"></a>Åsidosätta låstillstånd

Det är vanligtvis möjligt för någon med lämplig [rollbaserad åtkomstkontroll](../../../role-based-access-control/overview.md) (RBAC) på prenumerationen, till exempel ägarrollen, att tillåtas att ändra eller ta bort en resurs. Den här åtkomsten är inte fallet när Azure Blueprints tillämpar låsning som en del av en distribuerad tilldelning. Om tilldelningen har angetts med alternativet **Skrivskyddat** eller **Ta inte bort** kan inte ens prenumerationsägaren utföra den blockerade åtgärden på den skyddade resursen.

Den här säkerhetsåtgärden skyddar konsekvensen hos den definierade skissen och miljön som den har utformats för att skapa från oavsiktlig eller programmatisk borttagning eller ändring.

### <a name="assign-at-management-group"></a>Tilldela i hanteringsgrupp

Ett ytterligare alternativ för att förhindra att prenumerationsägare tar bort en skisstilldelning är att tilldela skissen till en hanteringsgrupp. I det här fallet är det bara **ägare** till hanteringsgruppen som har de behörigheter som krävs för att ta bort skisstilldelningen.

Om du vill tilldela skissen till en hanteringsgrupp i stället för en prenumeration ändras REST API-anropet så här:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

Hanteringsgruppen som `{assignmentMG}` definieras av måste vara antingen i hanteringsgrupphierarkin eller vara samma hanteringsgrupp där skissdefinitionen sparas.

Förfråmstexten för skisstilldelningen ser ut så här:

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
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

Den viktigaste skillnaden i den här begärandetexten `properties.scope` och en som tilldelas en prenumeration är egenskapen. Den här obligatoriska egenskapen måste anges till den prenumeration som skisstilldelningen gäller för. Prenumerationen måste vara ett direkt underordnat i hanteringsgrupphierarkin där skisstilldelningen lagras.

> [!NOTE]
> En skiss som tilldelats hanteringsgruppscopet fungerar fortfarande som en skisstilldelning på prenumerationsnivå. Den enda skillnaden är var skisstilldelningen lagras för att förhindra att prenumerationsägare tar bort tilldelningen och tillhörande lås.

## <a name="removing-locking-states"></a>Ta bort låstillstånd

Om det blir nödvändigt att ändra eller ta bort en resurs som skyddas av en tilldelning finns det två sätt att göra det.

- Uppdatera skisstilldelningen till ett låsläge **i Lås inte**
- Ta bort skisstilldelningen

När tilldelningen tas bort tas låsen som skapats av Azure Blueprints bort. Resursen lämnas dock kvar och måste tas bort på normalt sätt.

## <a name="how-blueprint-locks-work"></a>Så här fungerar skisslås

En RBAC [neka tilldelningar](../../../role-based-access-control/deny-assignments.md) neka åtgärd tillämpas på artefaktresurser under tilldelning av en skiss om tilldelningen valde alternativet **Skrivskyddat** eller **Ta inte bort.** Åtgärden neka läggs till av den hanterade identiteten för skisstilldelningen och kan endast tas bort från artefaktresurserna av samma hanterade identitet. Den här säkerhetsåtgärden tillämpar låsmekanismen och förhindrar att skisslåset tas bort utanför Azure Blueprints.

:::image type="content" source="../media/resource-locking/blueprint-deny-assignment.png" alt-text="Skiss neka tilldelning i resursgrupp" border="false":::

Egenskaperna [för neka tilldelning](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) för varje läge är följande:

|Läge |Behörigheter.Åtgärder |Behörigheter.Åtgärder inte |Rektorer[i]. Typ |ExcludePrincipals[i]. Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Skrivskydd |**\*** |**\*/läs** |SystemDedefinierad (alla) |skisstilldelning och användardefinierad i **exkluderadeprinciper** |Resursgrupp - _sant_; Resurs - _falskt_ |
|Ta inte bort |**\*/delete** | |SystemDedefinierad (alla) |skisstilldelning och användardefinierad i **exkluderadeprinciper** |Resursgrupp - _sant_; Resurs - _falskt_ |

> [!IMPORTANT]
> Azure Resource Manager cachelagrar information om rolltilldelning i upp till 30 minuter. Därför kan det hända att neka tilldelningar som förnekar åtgärdens på skissresurser kanske inte omedelbart är i full effekt. Under den här tidsperioden kan det vara möjligt att ta bort en resurs som är avsedd att skyddas av skisslås.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Utesluta ett huvudnamn från en neka tilldelning

I vissa design- eller säkerhetsscenarier kan det vara nödvändigt att utesluta ett huvudnamn från neka [tilldelningen](../../../role-based-access-control/deny-assignments.md) som skisstilldelningen skapar. Det här steget görs i REST API genom att lägga till upp till fem värden i arrayen **exkluderadeprincipaler** i egenskapen **locks** när [tilldelningen skapas](/rest/api/blueprints/assignments/createorupdate). Följande tilldelningsdefinition är ett exempel på ett begärandeorgan som innehåller **uteslutnaPrinciper:**

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

## <a name="exclude-an-action-from-a-deny-assignment"></a>Utesluta en åtgärd från en neka tilldelning

I likhet med att [utesluta ett huvudnamn](#exclude-a-principal-from-a-deny-assignment) på en [neka tilldelning](../../../role-based-access-control/deny-assignments.md) i en skisstilldelning kan du utesluta specifika [RBAC-åtgärder](../../../role-based-access-control/resource-provider-operations.md). Inom **properties.locks-blocket** kan en **excludedActions** läggas till på samma plats som **excludedPrincipals** är:

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

Även **om uteslutnaPrinciper** måste vara explicita, kan **excludedActions-poster** använda `*` för jokerteckenmatchning av RBAC-åtgärder.

## <a name="next-steps"></a>Nästa steg

- Följ [självstudien för att skydda nya resurser.](../tutorials/protect-new-resources.md)
- Läs mer om [skisslivscykeln](lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](sequencing-order.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).
- Lös problem under tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).