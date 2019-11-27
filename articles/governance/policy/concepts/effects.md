---
title: Förstå hur effekter fungerar
description: Azure Policy definitioner har olika effekter som avgör hur efterlevnaden hanteras och rapporteras.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 8338f3bf965f121a553a56c551d2095bf60e4880
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279513"
---
# <a name="understand-azure-policy-effects"></a>Förstå effekterna av Azure Policy

Varje princip i Azure Policy har en enda effekt. Denna påverkan anger vad som händer när principregeln utvärderas så att de matchar. Effekterna beter sig annorlunda om de är för en ny resurs, en uppdaterad resurs eller en befintlig resurs.

Dessa effekter stöds för närvarande i en princip definition:

- [Slå](#append)
- [Granska](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Autentiseringsregel](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Inaktiverad](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) (för hands version)
- [EnforceRegoPolicy](#enforceregopolicy) (för hands version)
- [Gör](#modify)

## <a name="order-of-evaluation"></a>Ordningen för utvärdering

Begär Anden om att skapa eller uppdatera en resurs via Azure Resource Manager utvärderas av Azure Policy först. Azure Policy skapar en lista med alla tilldelningar som gäller för resursen och utvärderar sedan resursen mot varje definition. Azure Policy bearbetar flera av effekterna innan du skickar begäran till rätt resurs leverantör. Detta förhindrar onödig bearbetning av en resurs leverantör när en resurs inte uppfyller de design kontroller som Azure Policy.

- **Inaktiverat** kontrol leras först för att avgöra om princip regeln ska utvärderas.
- **Lägg till** och **ändra** utvärderas sedan. Eftersom antingen kan ändra begäran kan en ändring som gjorts förhindra att en granskning eller nekas från att utlösas.
- **Deny** utvärderas sedan. Neka innan granskningen, dubbla loggning för en oönskad resurs förhindras genom att utvärdera.
- **Granskningen** utvärderas sedan innan begäran skickas till resurs leverantören.

När resurs leverantören returnerar en lyckad kod, utvärderas **AuditIfNotExists** och **DeployIfNotExists** för att avgöra om det krävs ytterligare loggning eller åtgärd av efterlevnad.

Det finns för närvarande ingen utvärderings ordning för **EnforceOPAConstraint** -eller **EnforceRegoPolicy** -effekterna.

## <a name="disabled"></a>Disabled

Detta är användbart för att testa situationer eller för när principdefinitionen har parameteriserat effekten. Den här flexibiliteten gör det möjligt att inaktivera en enskild tilldelning i stället för att inaktivera alla tilldelningar för den principen.

Ett alternativ till den inaktiverade inställningen är **enforcementMode** som anges för princip tilldelningen.
När **enforcementMode** är _inaktive rad_utvärderas resurserna fortfarande. Loggning, till exempel aktivitets loggar och princip påverkan inträffar inte. Mer information finns i [princip tilldelning – tvingande läge](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Lägg till

Lägg till används för att lägga till fler fält till den begärda resursen under generering och uppdatering. Ett vanligt exempel är att ange tillåtna IP-adresser för en lagrings resurs.

> [!IMPORTANT]
> Append är avsett för användning med egenskaper som inte är taggar. När Lägg till kan lägga till taggar till en resurs under en Create-eller Update-begäran, rekommenderar vi att du använder taggen [ändra](#modify) i stället.

### <a name="append-evaluation"></a>Lägga till utvärderingen

Lägg till utvärderar innan begäran bearbetas av en Resursprovider under skapandet eller uppdatering av en resurs. Lägg till fält till resursen **när villkors** villkoret för princip regeln är uppfyllt. Om Lägg till effekten skulle åsidosätter ett värde i den ursprungliga begäran med ett annat värde kan sedan den fungerar som en nekandeeffekt och avvisar begäran. Om du vill lägga till ett nytt värde i en befintlig matris använder du **[\*]** -versionen av aliaset.

När en principdefinition med effekten append körs som en del av en utvärderingscykel, göra den inte ändringar i resurser som redan finns. I stället markeras alla resurser som uppfyller **IF** -villkoret som icke-kompatibel.

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

Ändra används för att lägga till, uppdatera eller ta bort taggar på en resurs under skapandet eller uppdateringen. Ett vanligt exempel är att uppdatera taggar på resurser som costCenter. En ändrings princip bör alltid ha `mode` angetts till _indexerad_ om inte mål resursen är en resurs grupp. Befintliga icke-kompatibla resurser kan åtgärdas med en [reparations uppgift](../how-to/remediate-resources.md). En enda ändra-regel kan ha valfritt antal åtgärder.

> [!IMPORTANT]
> Ändra är för närvarande endast för användning med-taggar. Om du hanterar Taggar rekommenderar vi att du använder ändra i stället för Lägg till som ändra och ger ytterligare åtgärds typer och möjlighet att åtgärda befintliga resurser. Tillägg rekommenderas dock om du inte kan skapa en hanterad identitet.

### <a name="modify-evaluation"></a>Ändra utvärdering

Ändra utvärderar innan begäran bearbetas av en resurs leverantör under skapandet eller uppdateringen av en resurs. Ändra taggar för tillägg eller uppdateringar på en resurs när **villkors** villkoret för princip regeln är uppfyllt.

När en princip definition med hjälp av ändra-effekter körs som en del av en utvärderings cykel, gör den inte några ändringar i resurser som redan finns. I stället markeras alla resurser som uppfyller **IF** -villkoret som icke-kompatibel.

### <a name="modify-properties"></a>Ändra egenskaper

Egenskapen **information** för funktionen ändra har alla under egenskaper som definierar de behörigheter som krävs för reparation och de **åtgärder** som används för att lägga till, uppdatera eller ta bort taggattribut.

- **roleDefinitionIds** [krävs]
  - Den här egenskapen måste innehålla en matris med strängar som matchar rollbaserad åtkomstkontroll roll-ID nås av prenumerationen. Mer information finns i [reparation-Konfigurera princip definition](../how-to/remediate-resources.md#configure-policy-definition).
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

- Ställer in `environment`-taggen på "test", även om den redan finns med ett annat värde.
- Tar bort taggen `TempResource`.
- Ställer in `Dept`-taggen till den princip parameter _DeptName_ som kon figurer ATS för princip tilldelningen.

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

Exempel 2: ta bort taggen `env` och Lägg till `environment`-taggen eller ersätt befintliga `environment`-Taggar med ett parameter värde:

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

Neka används för att förhindra att en resursbegäran som matchar inte definierad standarder genom en principdefinition och misslyckas begäran.

### <a name="deny-evaluation"></a>Neka utvärdering

När skapar eller uppdaterar en matchande resurs neka förhindrar att begäran innan den skickas till Resursprovidern. Begäran returneras som en `403 (Forbidden)`. I portalen kan förbjudet visas som en status för distributionen förhindrades av principtilldelningen.

Under utvärdering av befintliga resurser markeras resurser som matchar en neka-principdefinition som icke-kompatibla.

### <a name="deny-properties"></a>Neka egenskaper

Neka-funktionen har inga ytterligare egenskaper att använda i **then** -villkoret för princip definitionen.

### <a name="deny-example"></a>Neka exempel

Exempel: Med nekandeeffekt.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Granska

Granska används för att skapa en varning-händelse i aktivitetsloggen vid utvärdering av en icke-kompatibel resurs, men slutar inte den begäran.

### <a name="audit-evaluation"></a>Granska utvärdering

Audit är den senaste effekterna som kontrol leras av Azure Policy när en resurs skapas eller uppdateras. Azure Policy skickar sedan resursen till resurs leverantören. Granskning fungerar på samma sätt för en resursbegäran och en utvärderingscykel för datorprincip. Azure Policy lägger till en `Microsoft.Authorization/policies/audit/action`-åtgärd i aktivitets loggen och markerar resursen som icke-kompatibel.

### <a name="audit-properties"></a>Egenskaper för granskning

Gransknings effekterna har inte några ytterligare egenskaper **som kan användas i villkors** definitionen.

### <a name="audit-example"></a>Granska exempel

Exempel: Med effekten granskning.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists möjliggör granskning av resurser som matchar **IF** -villkoret, men som inte har de komponenter som anges i **informationen** om **then** -villkoret.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists utvärdering

AuditIfNotExists körs när en Resursprovider hanterat en skapa eller uppdatera resursbegäran och returnerade statuskoden lyckades. Granskningen sker om det inte finns några relaterade resurser eller om resurserna som definieras av **ExistenceCondition** inte utvärderas till true. Azure Policy lägger till en `Microsoft.Authorization/policies/audit/action`-åtgärd i aktivitets loggen på samma sätt som gransknings resultatet. När den utlöses är den resurs som uppfyllde **IF** -villkoret den resurs som har marker ATS som icke-kompatibel.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists egenskaper

Egenskapen **information** för AuditIfNotExists-effekterna har alla under egenskaper som definierar de relaterade resurserna som ska matchas.

- **Typ** [obligatoriskt]
  - Anger typ av relaterade resurs så att de matchar.
  - Om **information. Type** är en resurs typ under **IF** -villkor-resursen, frågar principen efter resurser av den här **typen** inom omfånget för den utvärderade resursen. I annat fall är princip frågorna inom samma resurs grupp som den utvärderade resursen.
- **Namn** (valfritt)
  - Anger det exakta namnet på resursen som ska matcha och gör principen att hämta en specifik resurs i stället för alla resurser av den angivna typen.
  - När villkors värden för **IF. Field. Type** och **then. details. Type** match, blir **namnet** _obligatoriskt_ och måste vara `[field('name')]`. En [gransknings](#audit) funktion bör dock beaktas i stället.
- **ResourceGroupName** (valfritt)
  - Tillåter matchningen av relaterade resursen komma från en annan resursgrupp.
  - Gäller inte om **typen** är en resurs som skulle ligga under villkors resursen **IF** .
  - Standard är resurs gruppen **om** villkors resursen.
- **ExistenceScope** (valfritt)
  - Tillåtna värden är _prenumerations_ -och _ResourceGroup_.
  - Anger vilket scope för vart du ska hämta relaterade resursen som ska matcha från.
  - Gäller inte om **typen** är en resurs som skulle ligga under villkors resursen **IF** .
  - För _ResourceGroup_begränsas till resurs gruppen **om** villkors resursen eller resurs gruppen som anges i **ResourceGroupName**.
  - För _prenumerationen_frågar hela prenumerationen för den relaterade resursen.
  - Standardvärdet är _ResourceGroup_.
- **ExistenceCondition** (valfritt)
  - Om inget annat anges uppfyller en relaterad resurs av **typen** effekterna och utlöser inte granskningen.
  - Använder samma språk som princip regeln för **IF** -villkoret, men utvärderas mot varje relaterad resurs individuellt.
  - Om alla matchande relaterade resurser utvärderas till SANT, effekten har uppfyllts och utlöses inte granskningen.
  - Kan använda [Field ()] för att kontrol lera likvärdighet med värden i **IF** -villkoret.
  - Kan till exempel användas för att kontrol lera att den överordnade resursen (i **IF** -villkoret) finns på samma resurs plats som den matchande relaterade resursen.

### <a name="auditifnotexists-example"></a>AuditIfNotExists exempel

Exempel: Utvärderar virtuella datorer för att avgöra om tillägg för program mot skadlig kod finns, så granskar om det saknas.

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

DeployIfNotExists körs när en Resursprovider hanterat en skapa eller uppdatera resursbegäran och returnerade statuskoden lyckades. En mall distribution sker om det inte finns några relaterade resurser eller om resurserna som definieras av **ExistenceCondition** inte utvärderas till true.

Under en utvärderingscykel principdefinitioner med effekten DeployIfNotExists som matchar resurser markeras som icke-kompatibel, men ingen åtgärd utförs på den här resursen.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists-egenskaper

Egenskapen **information** för DeployIfNotExists-effekterna har alla under egenskaper som definierar de relaterade resurserna som ska matchas och mallen som ska köras.

- **Typ** [obligatoriskt]
  - Anger typ av relaterade resurs så att de matchar.
  - Startar genom att försöka hämta en resurs under villkors resursen **IF** , och sedan frågar i samma resurs grupp som villkors resursen **om** .
- **Namn** (valfritt)
  - Anger det exakta namnet på resursen som ska matcha och gör principen att hämta en specifik resurs i stället för alla resurser av den angivna typen.
  - När villkors värden för **IF. Field. Type** och **then. details. Type** match, blir **namnet** _obligatoriskt_ och måste vara `[field('name')]`.
- **ResourceGroupName** (valfritt)
  - Tillåter matchningen av relaterade resursen komma från en annan resursgrupp.
  - Gäller inte om **typen** är en resurs som skulle ligga under villkors resursen **IF** .
  - Standard är resurs gruppen **om** villkors resursen.
  - Om en malldistributionen körs har den distribuerats i resursgruppen för det här värdet.
- **ExistenceScope** (valfritt)
  - Tillåtna värden är _prenumerations_ -och _ResourceGroup_.
  - Anger vilket scope för vart du ska hämta relaterade resursen som ska matcha från.
  - Gäller inte om **typen** är en resurs som skulle ligga under villkors resursen **IF** .
  - För _ResourceGroup_begränsas till resurs gruppen **om** villkors resursen eller resurs gruppen som anges i **ResourceGroupName**.
  - För _prenumerationen_frågar hela prenumerationen för den relaterade resursen.
  - Standardvärdet är _ResourceGroup_.
- **ExistenceCondition** (valfritt)
  - Om inget annat anges uppfyller en relaterad resurs av **typen** effekterna och utlöser inte distributionen.
  - Använder samma språk som princip regeln för **IF** -villkoret, men utvärderas mot varje relaterad resurs individuellt.
  - Om alla matchande relaterade resurser utvärderas till SANT, effekten har uppfyllts och utlöses inte distributionen.
  - Kan använda [Field ()] för att kontrol lera likvärdighet med värden i **IF** -villkoret.
  - Kan till exempel användas för att kontrol lera att den överordnade resursen (i **IF** -villkoret) finns på samma resurs plats som den matchande relaterade resursen.
- **roleDefinitionIds** [krävs]
  - Den här egenskapen måste innehålla en matris med strängar som matchar rollbaserad åtkomstkontroll roll-ID nås av prenumerationen. Mer information finns i [reparation-Konfigurera princip definition](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (valfritt)
  - Tillåtna värden är _prenumerations_ -och _ResourceGroup_.
  - Anger vilken typ av distribution som ska utlösas. _Prenumerationen_ anger en [distribution på prenumerations nivå](../../../azure-resource-manager/deploy-to-subscription.md), _ResourceGroup_ anger en distribution till en resurs grupp.
  - En _plats_ egenskap måste anges i _distributionen_ när du använder distributioner på prenumerations nivå.
  - Standardvärdet är _ResourceGroup_.
- **Distribution** [krävs]
  - Den här egenskapen ska innehålla den fullständiga mal Lav distributionen som den skulle skickas till `Microsoft.Resources/deployments` skicka API. Mer information finns i [distributioner REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Alla funktioner i **distributions** egenskapen utvärderas som komponenter i mallen, inte principen. Undantaget är **parameter** egenskapen som skickar värden från principen till mallen. **Värdet** i det här avsnittet under ett Mallnamn används för att utföra det här värdet genom att skicka (se _fullDbName_ i DeployIfNotExists-exemplet).

### <a name="deployifnotexists-example"></a>DeployIfNotExists-exempel

Exempel: Utvärderar SQL Server-databaser för att avgöra om transparentDataEncryption är aktiverad. Annars körs en distribution som ska aktive ras.

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

## <a name="enforceopaconstraint"></a>EnforceOPAConstraint

Den här inställningen används med ett princip definitions *läge* för `Microsoft.Kubernetes.Data`. Den används för att skicka Gatekeeper v3-regler för åtkomst kontroll som definierats med [OPA constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) till att [Öppna princip agent](https://www.openpolicyagent.org/) (OPA) till självhanterade Kubernetes-kluster i Azure.

> [!NOTE]
> [Azure policy för AKS-motorn](aks-engine.md) finns i en offentlig för hands version och stöder bara inbyggda princip definitioner.

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint-utvärdering

Den öppna princip agentens åtkomst kontroll utvärderar alla nya begär anden i klustret i real tid.
Var 5: e minut slutförs en fullständig genomsökning av klustret och resultaten rapporteras till Azure Policy.

### <a name="enforceopaconstraint-properties"></a>Egenskaper för EnforceOPAConstraint

Egenskapen **information** för EnforceOPAConstraint-effekter har de subegenskaper som beskriver Gatekeeper-åtkomstkontroll för åtkomst kontroll.

- **constraintTemplate** [krävs]
  - Begränsnings mal len CustomResourceDefinition (CRD) som definierar nya begränsningar. Mallen definierar Rego Logic, begränsnings schema och villkors parametrar som skickas via **värden** från Azure policy.
- **begränsning** [obligatoriskt]
  - CRD-implementeringen av begränsnings mal len. Använder parametrar som skickas via **värden** som `{{ .Values.<valuename> }}`. I exemplet nedan är detta `{{ .Values.cpuLimit }}` och `{{ .Values.memoryLimit }}`.
- **värden** [valfritt]
  - Definierar alla parametrar och värden som ska skickas till begränsningen. Varje värde måste finnas i CRD för begränsnings mal len.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy-exempel

Exempel: Gatekeeper v3-åtkomstkontroll för att ange behållarens processor gränser och minnes resurs gränser i AKS-motorn.

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "in": [
                "Microsoft.ContainerService/managedClusters",
                "AKS Engine"
            ]
        },
        {
            "field": "location",
            "equals": "westus2"
        }
    ]
},
"then": {
    "effect": "enforceOPAConstraint",
    "details": {
        "constraintTemplate": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/template.yaml",
        "constraint": "https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-resource-limits/constraint.yaml",
        "values": {
            "cpuLimit": "[parameters('cpuLimit')]",
            "memoryLimit": "[parameters('memoryLimit')]"
        }
    }
}
```

## <a name="enforceregopolicy"></a>EnforceRegoPolicy

Den här inställningen används med ett princip definitions *läge* för `Microsoft.ContainerService.Data`. Den används för att skicka Gatekeeper v2-regler för åtkomst kontroll som definierats med [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) för att [Öppna princip agent](https://www.openpolicyagent.org/) (OPA) på [Azure Kubernetes-tjänsten](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure policy för AKS](rego-for-aks.md) är i begränsad för hands version och stöder bara inbyggda princip definitioner

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy-utvärdering

Den öppna princip agentens åtkomst kontroll utvärderar alla nya begär anden i klustret i real tid.
Var 5: e minut slutförs en fullständig genomsökning av klustret och resultaten rapporteras till Azure Policy.

### <a name="enforceregopolicy-properties"></a>Egenskaper för EnforceRegoPolicy

Egenskapen **information** för EnforceRegoPolicy-funktionen har de subegenskaper som beskriver åtkomst kontroll regeln för gatekeeper v2.

- **policyId** [krävs]
  - Ett unikt namn som skickas som en parameter till Rego-åtkomstkontroll.
- **princip** [nödvändig]
  - Anger URI för Rego-åtkomstkontroll.
- **policyParameters** [valfritt]
  - Definierar alla parametrar och värden som ska skickas till Rego-principen.

### <a name="enforceregopolicy-example"></a>EnforceRegoPolicy-exempel

Exempel: Gatekeeper v2-åtkomstkontroll för att endast tillåta de angivna behållar avbildningarna i AKS.

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

## <a name="layering-policies"></a>Skikta principer

En resurs kan påverkas av flera tilldelningar. Dessa tilldelningar kan vara i samma definitionsområde eller i olika omfång. Var och en av de här tilldelningarna troligtvis även har en annan effekt som definierats. Villkor och effekt för varje princip utvärderas oberoende av varandra. Till exempel:

- Principen 1
  - Begränsar resursplats till 'westus'
  - Tilldelas prenumeration A
  - Neka effekt
- Princip för 2
  - Begränsar resursplats till ”eastus”
  - Tilldelad till resursgruppen B i prenumeration A
  - Spårningsseffekt
  
Den här konfigurationen skulle resultera i följande resultat:

- Resurser redan i resursgruppen B i ”eastus” är kompatibla med principen 2 och icke-kompatibla principen 1
- Alla resurser redan i resursgruppen B inte i ”eastus” är icke-kompatibla principen 2 och icke-kompatibla principen 1 om den inte i ”westus”
- Någon ny resurs i prenumeration A inte är i ”westus' nekas av principen 1
- Någon ny resurs i prenumeration A och B-resursgrupp i 'westus' har skapats och icke-kompatibla på princip 2

Om både 1 och 2 i hade kraft av neka, situationen ändras till:

- Alla resurser redan i resursgruppen B inte i ”eastus” är icke-kompatibla principen 2
- Alla resurser redan i resursgruppen B inte i ”westus” är icke-kompatibla principen 1
- Någon ny resurs i prenumeration A inte är i ”westus' nekas av principen 1
- Alla nya resurser i resursgruppen B i prenumerationen som en nekad

Varje tilldelning utvärderas individuellt. Därför det är inte en möjlighet för en resurs för försening via ett uppehåll av skillnader i omfånget. Netto resultatet av lager principer eller princip överlappningar anses vara **ackumulerad mest restriktiv**. Till exempel om båda princip 1 och 2 hade en nekandeeffekt, blockeras en resurs av överlappande och motstridiga principer. Om du fortfarande behöver resursen ska påverkar skapas i målområdet, granska undantag vid varje uppgift att verifiera rätt principer rätt scope.

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](definition-structure.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
