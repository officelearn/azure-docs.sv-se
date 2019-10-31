---
title: Förstå hur effekter fungerar
description: Azure Policy definitioner har olika effekter som avgör hur efterlevnaden hanteras och rapporteras.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/17/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 4f657cd8c804a597220a7e74d1fce0401c4cd9ae
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176333"
---
# <a name="understand-azure-policy-effects"></a>Förstå Azure Policys effekter

Varje princip definition i Azure Policy har en enda påverkan. Den här inställningen avgör vad som händer när princip regeln utvärderas för matchning. Effekterna fungerar annorlunda om de är för en ny resurs, en uppdaterad resurs eller en befintlig resurs.

Dessa effekter stöds för närvarande i en princip definition:

- [Slå](#append)
- [Granska](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Autentiseringsregel](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Inaktiverad](#disabled)
- [EnforceRegoPolicy](#enforceregopolicy) (för hands version)
- [Gör](#modify)

## <a name="order-of-evaluation"></a>Utvärderings ordning

Begär Anden om att skapa eller uppdatera en resurs via Azure Resource Manager utvärderas av Azure Policy först. Azure Policy skapar en lista med alla tilldelningar som gäller för resursen och utvärderar sedan resursen mot varje definition. Azure Policy bearbetar flera av effekterna innan du skickar begäran till rätt resurs leverantör. Detta förhindrar onödig bearbetning av en resurs leverantör när en resurs inte uppfyller de design kontroller som Azure Policy.

- **Inaktiverat** kontrol leras först för att avgöra om princip regeln ska utvärderas.
- **Lägg till** och **ändra** utvärderas sedan. Eftersom antingen kan ändra begäran kan en ändring som gjorts förhindra att en granskning eller nekas från att utlösas.
- **Deny** utvärderas sedan. Genom att utvärdera neka före granskning, förhindras dubbel loggning av en oönskad resurs.
- **Granskningen** utvärderas sedan innan begäran skickas till resurs leverantören.

När resurs leverantören returnerar en lyckad kod, utvärderas **AuditIfNotExists** och **DeployIfNotExists** för att avgöra om det krävs ytterligare loggning eller åtgärd av efterlevnad.

Det finns för närvarande ingen utvärderings ordning för **EnforceRegoPolicy** -påverkan.

## <a name="disabled"></a>Disabled

Den här inställningen är användbar för att testa situationer eller när princip definitionen har parameterstyrda påverkan. Den här flexibiliteten gör det möjligt att inaktivera en enskild tilldelning i stället för att inaktivera alla tilldelningar för principen.

Ett alternativ till den inaktiverade inställningen är **enforcementMode** som anges för princip tilldelningen.
När **enforcementMode** är _inaktive rad_utvärderas resurserna fortfarande. Loggning, till exempel aktivitets loggar och princip påverkan inträffar inte. Mer information finns i [princip tilldelning – tvingande läge](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Lägg till

Lägg till används för att lägga till ytterligare fält till den begärda resursen under skapandet eller uppdateringen. Ett vanligt exempel är att ange tillåtna IP-adresser för en lagrings resurs.

> [!IMPORTANT]
> Append är avsett för användning med egenskaper som inte är taggar. När Lägg till kan lägga till taggar till en resurs under en Create-eller Update-begäran, rekommenderar vi att du använder taggen [ändra](#modify) i stället.

### <a name="append-evaluation"></a>Lägg till utvärdering

Lägg till utvärderas innan begäran bearbetas av en resurs leverantör under skapandet eller uppdateringen av en resurs. Lägg till fält till resursen **när villkors** villkoret för princip regeln är uppfyllt. Om Lägg till-resultatet skulle åsidosätta ett värde i den ursprungliga begäran med ett annat värde, fungerar det som en nekande-inverkan och avvisar begäran. Om du vill lägga till ett nytt värde i en befintlig matris använder du **[\*]** -versionen av aliaset.

När en princip definition med hjälp av Lägg till-effekter körs som en del av en utvärderings cykel, gör den inte några ändringar i resurser som redan finns. I stället markeras alla resurser som uppfyller **IF** -villkoret som icke-kompatibel.

### <a name="append-properties"></a>Lägg till egenskaper

En Lägg till-funktion har endast en **informations** mat ris, vilket krävs. Som **information** är en matris kan den ta antingen ett enda **fält/värde-** par eller multipler. Se [definitions strukturen](definition-structure.md#fields) för listan över godkända fält.

### <a name="append-examples"></a>Lägg till exempel

Exempel 1: ett **fält/värde** -par med ett icke- **[\*]** - [alias](definition-structure.md#aliases) med ett mat ris **värde** som anger IP-regler för ett lagrings konto. När det icke- **[\*]** aliaset är en matris, lägger effekterna till **värdet** som hela matrisen. Om matrisen redan finns inträffar en Deny-händelse från konflikten.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
        "value": [{
            "action": "Allow",
            "value": "134.5.0.0/21"
        }]
    }]
}
```

Exempel 2: ett **fält/värde** -par med ett **[\*]** - [alias](definition-structure.md#aliases) med ett mat ris **värde** som anger IP-regler för ett lagrings konto. Genom att använda **[\*]** -aliaset lägger du till **värdet** i en befintlig matris som kan användas. Om matrisen inte finns kommer den att skapas.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]",
        "value": {
            "value": "40.40.40.40",
            "action": "Allow"
        }
    }]
}
```

## <a name="modify"></a>Ändra

Ändra används för att lägga till, uppdatera eller ta bort taggar på en resurs under skapandet eller uppdateringen. Ett vanligt exempel är att uppdatera taggar på resurser som costCenter. En ändra princip ska alltid ha `mode` inställt på _indexerad_ om inte mål resursen är en resurs grupp. Befintliga icke-kompatibla resurser kan åtgärdas med en [reparations uppgift](../how-to/remediate-resources.md). En enda ändra-regel kan ha valfritt antal åtgärder.

> [!IMPORTANT]
> Ändra är för närvarande endast för användning med-taggar. Om du hanterar Taggar rekommenderar vi att du använder ändra i stället för Lägg till som ändra och ger ytterligare åtgärds typer och möjlighet att åtgärda befintliga resurser. Tillägg rekommenderas dock om du inte kan skapa en hanterad identitet.

### <a name="modify-evaluation"></a>Ändra utvärdering

Ändra utvärderar innan begäran bearbetas av en resurs leverantör under skapandet eller uppdateringen av en resurs. Ändra taggar för tillägg eller uppdateringar på en resurs när **villkors** villkoret för princip regeln är uppfyllt.

När en princip definition med hjälp av ändra-effekter körs som en del av en utvärderings cykel, gör den inte några ändringar i resurser som redan finns. I stället markeras alla resurser som uppfyller **IF** -villkoret som icke-kompatibel.

### <a name="modify-properties"></a>Ändra egenskaper

Egenskapen **information** för funktionen ändra har alla under egenskaper som definierar de behörigheter som krävs för reparation och de **åtgärder** som används för att lägga till, uppdatera eller ta bort taggattribut.

- **roleDefinitionIds** [krävs]
  - Den här egenskapen måste innehålla en matris med strängar som matchar rollbaserad åtkomst kontroll roll-ID som är tillgängligt för prenumerationen. Mer information finns i [reparation-Konfigurera princip definition](../how-to/remediate-resources.md#configure-policy-definition).
  - Den roll som definieras måste innehålla alla åtgärder som beviljas rollen [deltagare](../../../role-based-access-control/built-in-roles.md#contributor) .
- **åtgärder** [krävs]
  - En matris med alla märknings åtgärder som ska utföras för matchande resurser.
  - Egenskaper:
    - **åtgärd** [krävs]
      - Definierar vilken åtgärd som ska vidtas för en matchande resurs. Alternativen är: _addOrReplace_, _Add_, _Remove_. _Lägg till_ fungerar ungefär som [i Lägg till-resultatet.](#append)
    - **fält** [obligatoriskt]
      - Taggen för att lägga till, ersätta eller ta bort. Taggnamn måste följa samma namngivnings konvention för andra [fält](./definition-structure.md#fields).
    - **värde** (valfritt)
      - Värdet som taggen ska ställas in på.
      - Den här egenskapen krävs om **åtgärden** är _addOrReplace_ eller _Add_.

### <a name="modify-operations"></a>Ändra åtgärder

Med egenskapen för **drifts** egenskaper kan du ändra flera taggar på olika sätt från en enda princip definition. Varje åtgärd består av egenskaperna **åtgärd**, **fält**och **värde** . Åtgärden avgör vad reparations uppgiften gör till taggarna, fältet avgör vilken tagg som ändras och värdet definierar den nya inställningen för taggen. Exemplet nedan gör följande tagg ändringar:

- Anger taggen `environment` till "test", även om den redan finns med ett annat värde.
- Tar bort taggen `TempResource`.
- Ställer in taggen `Dept` till den princip parameter _DeptName_ som kon figurer ATS i princip tilldelningen.

```json
"details": {
    ...
    "operations": [
        {
            "operation": "addOrReplace",
            "field": "tags['environment']",
            "value": "Test"
        },
        {
            "operation": "Remove",
            "field": "tags['TempResource']",
        },
        {
            "operation": "addOrReplace",
            "field": "tags['Dept']",
            "value": "[parameters('DeptName')]"
        }
    ]
}
```

Egenskapen **operation** har följande alternativ:

|Åtgärd |Beskrivning |
|-|-|
|addOrReplace |Lägger till den definierade taggen och värdet i resursen, även om taggen redan finns med ett annat värde. |
|Lägg till |Lägger till den definierade taggen och värdet i resursen. |
|Ta bort |Tar bort den definierade taggen från resursen. |

### <a name="modify-examples"></a>Ändra exempel

Exempel 1: Lägg till taggen `environment` och ersätt befintliga `environment`-Taggar med "test":

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "Test"
            }
        ]
    }
}
```

Exempel 2: ta bort taggen `env` och Lägg till taggen `environment` eller ersätt befintliga `environment`-Taggar med ett parameter värde:

```json
"then": {
    "effect": "modify",
    "details": {
        "roleDefinitionIds": [
            "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
        ],
        "operations": [
            {
                "operation": "Remove",
                "field": "tags['env']"
            },
            {
                "operation": "addOrReplace",
                "field": "tags['environment']",
                "value": "[parameters('tagValue')]"
            }
        ]
    }
}
```

## <a name="deny"></a>Neka

Neka används för att förhindra en resurs förfrågan som inte matchar definierade standarder via en princip definition och som Miss lyckas med begäran.

### <a name="deny-evaluation"></a>Neka utvärdering

När du skapar eller uppdaterar en matchad resurs förhindrar neka-begäran innan den skickas till resurs leverantören. Begäran returneras som en `403 (Forbidden)`. I portalen kan den förbjudna visas som status för den distribution som förhindrades av princip tilldelningen.

Vid utvärdering av befintliga resurser markeras resurser som matchar en definition för neka-principer som icke-kompatibla.

### <a name="deny-properties"></a>Egenskaper för neka

Neka-funktionen har inga ytterligare egenskaper att använda i **then** -villkoret för princip definitionen.

### <a name="deny-example"></a>Neka exempel

Exempel: använda neka-effekter.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Granska

Granskning används för att skapa en varnings händelse i aktivitets loggen när en icke-kompatibel resurs utvärderas, men begäran stoppas inte.

### <a name="audit-evaluation"></a>Gransknings utvärdering

Audit är den senaste effekterna som kontrol leras av Azure Policy när en resurs skapas eller uppdateras. Azure Policy skickar sedan resursen till resurs leverantören. Granskningen fungerar likadant för en resurs förfrågan och en utvärderings cykel. Azure Policy lägger till en `Microsoft.Authorization/policies/audit/action`-åtgärd i aktivitets loggen och markerar resursen som icke-kompatibel.

### <a name="audit-properties"></a>Gransknings egenskaper

Gransknings effekterna har inte några ytterligare egenskaper **som kan användas i villkors** definitionen.

### <a name="audit-example"></a>Gransknings exempel

Exempel: använda gransknings funktionen.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists möjliggör granskning av resurser som matchar **IF** -villkoret, men som inte har de komponenter som anges i **informationen** om **then** -villkoret.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists-utvärdering

AuditIfNotExists körs efter att en resurs leverantör har hanterat en begäran om att skapa eller uppdatera resurs och returnerat en status kod som lyckats. Granskningen sker om det inte finns några relaterade resurser eller om resurserna som definieras av **ExistenceCondition** inte utvärderas till true. Azure Policy lägger till en `Microsoft.Authorization/policies/audit/action`-åtgärd i aktivitets loggen på samma sätt som gransknings resultatet. När den utlöses är den resurs som uppfyllde **IF** -villkoret den resurs som har marker ATS som icke-kompatibel.

### <a name="auditifnotexists-properties"></a>Egenskaper för AuditIfNotExists

Egenskapen **information** för AuditIfNotExists-effekterna har alla under egenskaper som definierar de relaterade resurserna som ska matchas.

- **Typ** [obligatoriskt]
  - Anger vilken typ av relaterad resurs som ska matchas.
  - Om **information. Type** är en resurs typ under **IF** -villkor-resursen, frågar principen efter resurser av den här **typen** inom omfånget för den utvärderade resursen. I annat fall är princip frågorna inom samma resurs grupp som den utvärderade resursen.
- **Namn** (valfritt)
  - Anger det exakta namnet på resursen som ska matchas och gör att principen hämtar en specifik resurs i stället för alla resurser av den angivna typen.
  - När villkors värden för **IF. Field. Type** och **then. details. Type** match, blir **namnet** _obligatoriskt_ och måste vara `[field('name')]`. En [gransknings](#audit) funktion bör dock beaktas i stället.
- **ResourceGroupName** (valfritt)
  - Tillåter matchning av den relaterade resursen att komma från en annan resurs grupp.
  - Gäller inte om **typen** är en resurs som skulle ligga under villkors resursen **IF** .
  - Standard är resurs gruppen **om** villkors resursen.
- **ExistenceScope** (valfritt)
  - Tillåtna värden är _prenumerations_ -och _ResourceGroup_.
  - Anger omfånget för var den relaterade resursen ska hämtas för matchning från.
  - Gäller inte om **typen** är en resurs som skulle ligga under villkors resursen **IF** .
  - För _ResourceGroup_begränsas till resurs gruppen **om** villkors resursen eller resurs gruppen som anges i **ResourceGroupName**.
  - För _prenumerationen_frågar hela prenumerationen för den relaterade resursen.
  - Standardvärdet är _ResourceGroup_.
- **ExistenceCondition** (valfritt)
  - Om inget annat anges uppfyller en relaterad resurs av **typen** effekterna och utlöser inte granskningen.
  - Använder samma språk som princip regeln för **IF** -villkoret, men utvärderas mot varje relaterad resurs individuellt.
  - Om en matchande relaterad resurs utvärderas som sant är resultatet uppfyllt och utlöser inte granskningen.
  - Kan använda [Field ()] för att kontrol lera likvärdighet med värden i **IF** -villkoret.
  - Kan till exempel användas för att kontrol lera att den överordnade resursen (i **IF** -villkoret) finns på samma resurs plats som den matchande relaterade resursen.

### <a name="auditifnotexists-example"></a>AuditIfNotExists-exempel

Exempel: utvärderar Virtual Machines för att avgöra om tillägget för program mot skadlig kod finns, granskas när de saknas.

```json
{
    "if": {
        "field": "type",
        "equals": "Microsoft.Compute/virtualMachines"
    },
    "then": {
        "effect": "auditIfNotExists",
        "details": {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "existenceCondition": {
                "allOf": [{
                        "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                        "equals": "Microsoft.Azure.Security"
                    },
                    {
                        "field": "Microsoft.Compute/virtualMachines/extensions/type",
                        "equals": "IaaSAntimalware"
                    }
                ]
            }
        }
    }
}
```

## <a name="deployifnotexists"></a>DeployIfNotExists

På samma sätt som med AuditIfNotExists kör en DeployIfNotExists princip definition en mall distribution när villkoret är uppfyllt.

> [!NOTE]
> [Kapslade mallar](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) stöds med **deployIfNotExists**, men [länkade mallar](../../../azure-resource-manager/resource-group-linked-templates.md) stöds inte för närvarande.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists-utvärdering

DeployIfNotExists körs efter att en resurs leverantör har hanterat en begäran om att skapa eller uppdatera resurs och returnerat en status kod som lyckats. En mall distribution sker om det inte finns några relaterade resurser eller om resurserna som definieras av **ExistenceCondition** inte utvärderas till true.

Under en utvärderings cykel markeras princip definitioner med en DeployIfNotExists-påverkan som matchar resurserna som icke-kompatibel, men ingen åtgärd utförs på den resursen.

### <a name="deployifnotexists-properties"></a>Egenskaper för DeployIfNotExists

Egenskapen **information** för DeployIfNotExists-effekterna har alla under egenskaper som definierar de relaterade resurserna som ska matchas och mallen som ska köras.

- **Typ** [obligatoriskt]
  - Anger vilken typ av relaterad resurs som ska matchas.
  - Startar genom att försöka hämta en resurs under villkors resursen **IF** , och sedan frågar i samma resurs grupp som villkors resursen **om** .
- **Namn** (valfritt)
  - Anger det exakta namnet på resursen som ska matchas och gör att principen hämtar en specifik resurs i stället för alla resurser av den angivna typen.
  - När villkors värden för **IF. Field. Type** och **then. details. Type** match, blir **namnet** _obligatoriskt_ och måste vara `[field('name')]`.
- **ResourceGroupName** (valfritt)
  - Tillåter matchning av den relaterade resursen att komma från en annan resurs grupp.
  - Gäller inte om **typen** är en resurs som skulle ligga under villkors resursen **IF** .
  - Standard är resurs gruppen **om** villkors resursen.
  - Om en mall distribution körs, distribueras den i resurs gruppen för det här värdet.
- **ExistenceScope** (valfritt)
  - Tillåtna värden är _prenumerations_ -och _ResourceGroup_.
  - Anger omfånget för var den relaterade resursen ska hämtas för matchning från.
  - Gäller inte om **typen** är en resurs som skulle ligga under villkors resursen **IF** .
  - För _ResourceGroup_begränsas till resurs gruppen **om** villkors resursen eller resurs gruppen som anges i **ResourceGroupName**.
  - För _prenumerationen_frågar hela prenumerationen för den relaterade resursen.
  - Standardvärdet är _ResourceGroup_.
- **ExistenceCondition** (valfritt)
  - Om inget annat anges uppfyller en relaterad resurs av **typen** effekterna och utlöser inte distributionen.
  - Använder samma språk som princip regeln för **IF** -villkoret, men utvärderas mot varje relaterad resurs individuellt.
  - Om en matchande relaterad resurs utvärderas som sant är resultatet uppfyllt och utlöser inte distributionen.
  - Kan använda [Field ()] för att kontrol lera likvärdighet med värden i **IF** -villkoret.
  - Kan till exempel användas för att kontrol lera att den överordnade resursen (i **IF** -villkoret) finns på samma resurs plats som den matchande relaterade resursen.
- **roleDefinitionIds** [krävs]
  - Den här egenskapen måste innehålla en matris med strängar som matchar rollbaserad åtkomst kontroll roll-ID som är tillgängligt för prenumerationen. Mer information finns i [reparation-Konfigurera princip definition](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (valfritt)
  - Tillåtna värden är _prenumerations_ -och _ResourceGroup_.
  - Anger vilken typ av distribution som ska utlösas. _Prenumerationen_ anger en [distribution på prenumerations nivå](../../../azure-resource-manager/deploy-to-subscription.md), _ResourceGroup_ anger en distribution till en resurs grupp.
  - En _plats_ egenskap måste anges i _distributionen_ när du använder distributioner på prenumerations nivå.
  - Standardvärdet är _ResourceGroup_.
- **Distribution** [krävs]
  - Den här egenskapen ska innehålla den fullständiga mal Lav distributionen som den skulle skickas till `Microsoft.Resources/deployments`-PLACERINGs-API. Mer information finns i [distributioner REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Alla funktioner i **distributions** egenskapen utvärderas som komponenter i mallen, inte principen. Undantaget är **parameter** egenskapen som skickar värden från principen till mallen. **Värdet** i det här avsnittet under ett Mallnamn används för att utföra det här värdet genom att skicka (se _fullDbName_ i DeployIfNotExists-exemplet).

### <a name="deployifnotexists-example"></a>DeployIfNotExists-exempel

Exempel: utvärderar SQL Server databaser för att avgöra om transparentDataEncryption har Aktiver ATS. Annars körs en distribution som ska aktive ras.

```json
"if": {
    "field": "type",
    "equals": "Microsoft.Sql/servers/databases"
},
"then": {
    "effect": "DeployIfNotExists",
    "details": {
        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
        "name": "current",
        "roleDefinitionIds": [
            "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
            "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
        ],
        "existenceCondition": {
            "field": "Microsoft.Sql/transparentDataEncryption.status",
            "equals": "Enabled"
        },
        "deployment": {
            "properties": {
                "mode": "incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "fullDbName": {
                            "type": "string"
                        }
                    },
                    "resources": [{
                        "name": "[concat(parameters('fullDbName'), '/current')]",
                        "type": "Microsoft.Sql/servers/databases/transparentDataEncryption",
                        "apiVersion": "2014-04-01",
                        "properties": {
                            "status": "Enabled"
                        }
                    }]
                },
                "parameters": {
                    "fullDbName": {
                        "value": "[field('fullName')]"
                    }
                }
            }
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Den här inställningen används med ett princip definitions *läge* för `Microsoft.ContainerService.Data`. Den används för att skicka regler för åtkomst kontroll som definierats med [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) för att [Öppna princip agenten](https://www.openpolicyagent.org/) (OPA) på [Azure Kubernetes-tjänsten](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure policy för Kubernetes](rego-for-aks.md) finns i en offentlig för hands version och stöder bara inbyggda princip definitioner.

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy-utvärdering

Den öppna princip agentens åtkomst kontroll utvärderar alla nya begär anden i klustret i real tid.
Var 5: e minut slutförs en fullständig genomsökning av klustret och resultaten rapporteras till Azure Policy.

### <a name="enforceregopolicy-properties"></a>Egenskaper för EnforceRegoPolicy

Egenskapen **information** för EnforceRegoPolicy-effekter har de subegenskaper som beskriver reglerna för Rego-åtkomstkontroll.

- **policyId** [krävs]
  - Ett unikt namn som skickas som en parameter till Rego-åtkomstkontroll.
- **princip** [nödvändig]
  - Anger URI för Rego-åtkomstkontroll.
- **policyParameters** [valfritt]
  - Definierar alla parametrar och värden som ska skickas till Rego-principen.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy-exempel

Exempel: Rego-åtkomstkontroll för att endast tillåta de angivna behållar avbildningarna i AKS.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.ContainerService/managedClusters"
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "EnforceRegoPolicy",
    "details": {
        "policyId": "ContainerAllowedImages",
        "policy": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/KubernetesService/container-allowed-images/limited-preview/gatekeeperpolicy.rego",
        "policyParameters": {
            "allowedContainerImagesRegex": "[parameters('allowedContainerImagesRegex')]"
        }
    }
}
```

## <a name="layering-policies"></a>Skikt principer

En resurs kan påverkas av flera tilldelningar. Tilldelningarna kan finnas i samma omfång eller i olika omfång. Vart och ett av dessa tilldelningar är också troligt att en annan inverkan har definierats. Villkoret och påverkan för varje princip utvärderas oberoende av varandra. Exempel:

- Princip 1
  - Begränsar resursens plats till "väst"
  - Tilldelad till prenumeration A
  - Neka inverkan
- Princip 2
  - Begränsar resursens plats till "öster"
  - Tilldelad till resurs grupp B i prenumeration A
  - Gransknings funktion
  
Den här installationen skulle resultera i följande resultat:

- Alla resurser som redan finns i resurs grupp B i "öst" är kompatibla med princip 2 och icke-kompatibel till princip 1
- Alla resurser som redan finns i resurs grupp B som inte är östasiatiska är inte kompatibla med princip 2 och icke-kompatibla till princip 1 om inte i "väst"
- Alla nya resurser i prenumerationen som inte finns i "väst" nekas av princip 1
- Alla nya resurser i prenumeration A och resurs grupp B i "väst" skapas och är inkompatibla för princip 2

Om både princip 1 och princip 2 hade påverkan från neka ändras situationen till:

- Alla resurser som redan finns i resurs gruppen B som inte är östasiatiska är inte kompatibla med princip 2
- En resurs som redan finns i resurs grupp B som inte är "väst" är inte kompatibel med princip 1
- Alla nya resurser i prenumerationen som inte finns i "väst" nekas av princip 1
- Alla nya resurser i resurs grupp B för prenumeration A nekas

Varje tilldelning utvärderas individuellt. Det finns därför ingen möjlighet för en resurs att gå igenom ett mellanrum från olikheter i omfattningen. Netto resultatet av lager principer eller princip överlappningar anses vara **ackumulerad mest restriktiv**. Exempel: om både princip 1 och 2 hade en neka-påverkan blockeras en resurs av de överlappande och motstridiga principerna. Om du fortfarande behöver resursen som ska skapas i mål omfånget granskar du undantagen för varje tilldelning för att kontrol lera att rätt principer påverkar rätt omfång.

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](definition-structure.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/getting-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
