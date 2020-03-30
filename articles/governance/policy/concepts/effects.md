---
title: Förstå hur effekter fungerar
description: Azure Principdefinitioner har olika effekter som avgör hur efterlevnad hanteras och rapporteras.
ms.date: 03/23/2020
ms.topic: conceptual
ms.openlocfilehash: 631c941173a500a4159a37c7c31107b9a6eab872
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239974"
---
# <a name="understand-azure-policy-effects"></a>Förstå Azure-principeffekter

Varje principdefinition i Azure Policy har en enda effekt. Den effekten avgör vad som händer när principregeln utvärderas så att den matchar. Effekterna fungerar annorlunda om de är för en ny resurs, en uppdaterad resurs eller en befintlig resurs.

Dessa effekter stöds för närvarande i en principdefinition:

- [Lägg till](#append)
- [Granska](#audit)
- [AuditIfNotExists](#auditifnotexists)
- [Förneka](#deny)
- [DeployIfNotExists](#deployifnotexists)
- [Disabled](#disabled)
- [EnforceOPAConstraint](#enforceopaconstraint) (förhandsgranskning)
- [EnforceRegoPolicy](#enforceregopolicy) (förhandsgranskning)
- [Ändra](#modify)

## <a name="order-of-evaluation"></a>Utvärderingsordning

Begäranden om att skapa eller uppdatera en resurs via Azure Resource Manager utvärderas först av Azure Policy. Azure Policy skapar en lista över alla tilldelningar som gäller för resursen och utvärderar sedan resursen mot varje definition. Azure Policy bearbetar flera av effekterna innan begäran lämnas till lämplig resursprovider. Om du gör det förhindras onödig bearbetning av en resursprovider när en resurs inte uppfyller de utformade styrningskontrollerna för Azure Policy.

- **Inaktiverad** kontrolleras först för att avgöra om principregeln ska utvärderas.
- **Lägg till** och **ändra** utvärderas sedan. Eftersom endera kan ändra begäran kan en ändring förhindra att en revision eller neka verkan utlöses.
- **Deny** utvärderas sedan. Genom att utvärdera neka före granskning förhindras dubbel loggning av en oönskad resurs.
- **Granskning** utvärderas sedan innan begäran går till resursprovidern.

När resursprovidern har returnerat en lyckad kod **utvärderar AuditIfNotExists** och **DeployIfNotExists** för att avgöra om ytterligare efterlevnadsloggning eller åtgärder krävs.

Det finns för närvarande ingen utvärderingsordning för **VerkställopAConstraint-** **eller EnforceRegoPolicy-effekterna.**

## <a name="disabled"></a>Disabled

Den här effekten är användbar för att testa situationer eller för när principdefinitionen har parameteriserat effekten. Den här flexibiliteten gör det möjligt att inaktivera en enskild tilldelning i stället för att inaktivera alla den principens tilldelningar.

Ett alternativ till effekten Inaktiverad är **enforcementMode** som anges på principtilldelningen.
När **enforcementMode** är _Inaktiverat_utvärderas resurserna fortfarande. Loggning, till exempel aktivitetsloggar och principeffekten, inträffar inte. Mer information finns i [principtilldelning - tvingande läge](./assignment-structure.md#enforcement-mode).

## <a name="append"></a>Lägg till

Tillägg används för att lägga till ytterligare fält i den begärda resursen när den skapas eller uppdateras. Ett vanligt exempel är att ange tillåtna IP-adresser för en lagringsresurs.

> [!IMPORTANT]
> Append är avsedd att användas med egenskaper som inte är taggade. Lägg till kan lägga till taggar i en resurs under en begäran om att skapa eller uppdatera, men vi rekommenderar att du använder effekten [Ändra](#modify) för taggar i stället.

### <a name="append-evaluation"></a>Bifogad utvärdering

Lägg till utvärderar innan begäran bearbetas av en resursprovider när en resurs skapas eller uppdateras. Lägg till lägger till fält i resursen när **principregelns om-villkor** uppfylls. Om tilläggseffekten skulle åsidosätta ett värde i den ursprungliga begäran med ett annat värde fungerar den som en neka-effekt och avvisar begäran. Om du vill lägga till ett nytt värde i en befintlig matris använder du **[\*]** versionen av aliaset.

När en principdefinition med effekten lägg till körs som en del av en utvärderingscykel görs inga ändringar i resurser som redan finns. I stället markeras alla **if** resurser som uppfyller if-villkoret som icke-kompatibla.

### <a name="append-properties"></a>Lägg till egenskaper

En tilläggseffekt har **details** bara en detaljmatris, vilket krävs. Eftersom **information** är en matris kan det ta antingen ett enda **fält/värdepar** eller multiplar. Se [definitionsstrukturen](definition-structure.md#fields) för listan över godkända fält.

### <a name="append-examples"></a>Lägg till exempel

Exempel 1: Ett **enda fält/värdepar** med ett [alias](definition-structure.md#aliases) som inte**har ett\*** **matrisvärde** för att ange IP-regler för ett lagringskonto. När alias icke-**\*[ ]** är en matris, tillför effekten **värdet** som hela matrisen. Om matrisen redan finns inträffar en nekad händelse från konflikten.

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

Exempel 2: Ett **enda fält/värdepar** med ett **[\*]** [alias](definition-structure.md#aliases) med ett **matrisvärde** för att ange IP-regler för ett lagringskonto. Genom att använda alias **\*[ ]** lägger effekten till **värdet** i en potentiellt befintlig matris. Om matrisen inte finns ännu skapas den.

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

Ändra används för att lägga till, uppdatera eller ta bort taggar på en resurs när du skapar eller uppdaterar. Ett vanligt exempel är att uppdatera taggar på resurser som costCenter. En ändringsprincip `mode` ska alltid ha angetts till _Indexerad_ om inte målresursen är en resursgrupp. Befintliga resurser som inte uppfyller kraven kan åtgärdas med en [reparationsaktivitet](../how-to/remediate-resources.md). En enda ändringsregel kan ha valfritt antal åtgärder.

> [!IMPORTANT]
> Ändra är för närvarande endast för användning med taggar. Om du hanterar taggar rekommenderar vi att du använder Ändra i stället för Lägg till som Ändra ger ytterligare åtgärdstyper och möjlighet att åtgärda befintliga resurser. Lägg till rekommenderas dock om du inte kan skapa en hanterad identitet.

### <a name="modify-evaluation"></a>Ändra utvärdering

Ändra utvärderar innan begäran bearbetas av en resursprovider när en resurs skapas eller uppdateras. Ändra taggar för lägger till eller uppdaterar en resurs när **principregelns villkor** uppfylls om principregeln uppfylls.

När en principdefinition med effekten Ändra körs som en del av en utvärderingscykel görs inga ändringar i resurser som redan finns. I stället markeras alla **if** resurser som uppfyller if-villkoret som icke-kompatibla.

### <a name="modify-properties"></a>Ändra egenskaper

Egenskapen **details** för effekten Ändra har alla underegenskaper som definierar de behörigheter som behövs för reparation och de **åtgärder** som används för att lägga till, uppdatera eller ta bort taggvärden.

- **roleDefinitionIds** [krävs]
  - Den här egenskapen måste innehålla en matris med strängar som matchar rollbaserad rollkontrollroll-ID som är tillgängligt för prenumerationen. Mer information finns i [reparation - konfigurera principdefinition](../how-to/remediate-resources.md#configure-policy-definition).
  - Den definierade rollen måste omfatta alla åtgärder som beviljas rollen [Deltagare.](../../../role-based-access-control/built-in-roles.md#contributor)
- **operationer** [krävs]
  - En matris med alla taggåtgärder som ska slutföras på matchande resurser.
  - Egenskaper:
    - **åtgärden** [krävs]
      - Definierar vilken åtgärd som ska vidtas på en matchande resurs. Alternativen är: _addOrReplace_, _Lägg till_, _Ta bort_. _Lägg till_ fungerar som liknar effekten [Lägg](#append) till.
    - **fält** [obligatoriskt]
      - Taggen som ska läggas till, ersätta eller ta bort. Taggnamn måste följa samma namngivningskonvention för andra [fält](./definition-structure.md#fields).
    - **värde** (valfritt)
      - Det värde som taggen ska ställas in på.
      - Den här egenskapen krävs om **åtgärden** är _addOrReplace_ eller _Add_.

### <a name="modify-operations"></a>Ändra åtgärder

Egenskapsmatrisen **operationer** gör det möjligt att ändra flera taggar på olika sätt än en enda principdefinition. Varje operation består av **egenskaper för operation,** **fält**och **värde.** Åtgärden avgör vad reparationsuppgiften gör med taggarna, fältet avgör vilken tagg som ändras och värdet definierar den nya inställningen för taggen. I exemplet nedan görs följande taggändringar:

- Ställer `environment` in taggen på "Test", även om den redan finns med ett annat värde.
- Tar bort `TempResource`taggen .
- Anger `Dept` taggen till principparametern _DeptName_ som konfigurerats för principtilldelningen.

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

Exempel 1: `environment` Lägg till `environment` taggen och ersätt befintliga taggar med "Test":

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

Exempel 2: `env` Ta bort `environment` taggen och `environment` lägg till taggen eller ersätt befintliga taggar med ett parameteriserat värde:

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

Neka används för att förhindra en resursbegäran som inte matchar definierade standarder via en principdefinition och misslyckas begäran.

### <a name="deny-evaluation"></a>Neka utvärdering

När du skapar eller uppdaterar en matchad resurs förhindrar neka begäran innan den skickas till resursprovidern. Begäran returneras som `403 (Forbidden)`en . I portalen kan det förbjudna ses som en status för distributionen som förhindrades av principtilldelningen.

Under utvärderingen av befintliga resurser markeras resurser som matchar en neka principdefinition som icke-kompatibla.

### <a name="deny-properties"></a>Neka egenskaper

Neka-effekten har inga ytterligare egenskaper för användning i principdefinitionens **då.**

### <a name="deny-example"></a>Neka exempel

Exempel: Använda neka effekten.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Granska

Granskning används för att skapa en varningshändelse i aktivitetsloggen när du utvärderar en resurs som inte är kompatibel, men begäran stoppas inte.

### <a name="audit-evaluation"></a>Utvärdering av revision

Granskning är den sista effekten som kontrolleras av Azure Policy under skapandet eller uppdateringen av en resurs. Azure-princip skickar sedan resursen till resursleverantören. Granskning fungerar på samma sätt för en resursbegäran och en utvärderingscykel. Azure Policy `Microsoft.Authorization/policies/audit/action` lägger till en åtgärd i aktivitetsloggen och markerar resursen som icke-kompatibel.

### <a name="audit-properties"></a>Egenskaper för granskning

Granskningseffekten har inga ytterligare egenskaper för användning i principdefinitionens **då.**

### <a name="audit-example"></a>Exempel på granskning

Exempel: Använda granskningseffekten.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists aktiverar granskning av resurser som matchar if-villkoret, men har inte de komponenter som anges i **informationen om** **det dåvarande** villkoret. **if**

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists utvärdering

AuditIfNotExists körs efter att en resursprovider har hanterat en begäran om att skapa eller uppdatera resurser och har returnerat en statuskod för lyckade svar. Granskningen sker om det inte finns några relaterade resurser eller om de resurser som definieras av **ExistenceCondition** inte utvärderas till true. Azure Policy `Microsoft.Authorization/policies/audit/action` lägger till en åtgärd i aktivitetsloggen på samma sätt som granskningseffekten. När den utlöses är **if** resursen som uppfyllde om-villkoret den resurs som är markerad som icke-kompatibel.

### <a name="auditifnotexists-properties"></a>Egenskaper för AuditIfNotExister

Egenskapen **details** för effekterna AuditIfNotExists har alla underegenskaper som definierar de relaterade resurser som ska matchas.

- **Typ** [krävs]
  - Anger vilken typ av relaterad resurs som ska matchas.
  - Om **details.type** är en resurstyp under om-villkorsresursen, frågar principen efter resurser av den här **if** **typen** inom ramen för den utvärderade resursen. Annars principfrågor inom samma resursgrupp som den utvärderade resursen.
- **Namn** (valfritt)
  - Anger det exakta namnet på resursen som ska matchas och gör att principen hämtar en specifik resurs i stället för alla resurser av den angivna typen.
  - När villkorsvärdena för **if.field.type** och **then.details.type** _matchar,_ krävs och måste `[field('name')]` **namn** vara . En [revisionseffekt](#audit) bör dock övervägas i stället.
- **ResourceGroupName** (valfritt)
  - Gör att matchningen av den relaterade resursen kan komma från en annan resursgrupp.
  - Gäller inte om **typen** är en resurs som skulle vara under om-villkorresursen. **if**
  - Standard är **om** villkorsresursens resursgrupp.
- **ExistenceScope** (valfritt)
  - Tillåtna värden är _Prenumeration_ och _ResourceGroup_.
  - Anger var den relaterade resursen ska hämtas att matcha från.
  - Gäller inte om **typen** är en resurs som skulle vara under om-villkorresursen. **if**
  - För _ResourceGroup_, skulle begränsa till **om** villkor resurs resursgrupp eller resursgrupp som anges i **ResourceGroupName**.
  - För _Prenumeration_frågar hela prenumerationen för den relaterade resursen.
  - Standard är _ResourceGroup_.
- **ExistensVillkor** (valfritt)
  - Om inget anges uppfyller alla relaterade resurser av **typen** effekten och utlöser inte granskningen.
  - Använder samma språk som principregeln för IF-villkoret, men utvärderas mot varje relaterad resurs individuellt. **if**
  - Om någon matchande relaterad resurs utvärderas till true är effekten nöjd och utlöser inte granskningen.
  - Kan använda [field()] för att kontrollera likvärdighet med värden i **if-villkoret.**
  - Kan till exempel användas för att verifiera att **if** den överordnade resursen (i if-villkoret) finns på samma resursplats som den matchande relaterade resursen.

### <a name="auditifnotexists-example"></a>Exempel på AuditIfNotExists

Exempel: Utvärderar virtuella datorer för att avgöra om tillägget Mot skadlig kod finns sedan granskningar när det saknas.

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

I likhet med AuditIfNotExists körs en DeploymentIfNotExists-principdefinition en malldistribution när villkoret uppfylls.

> [!NOTE]
> [Kapslade mallar](../../../azure-resource-manager/templates/linked-templates.md#nested-template) stöds med **deployIfNotExists**, men [länkade mallar](../../../azure-resource-manager/templates/linked-templates.md#linked-template) stöds för närvarande inte.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists utvärdering

DeployIfNotExists körs ungefär 15 minuter efter att en resursprovider har hanterat en begäran om att skapa eller uppdatera resurser och har returnerat en statuskod för lyckade uppgifter. En malldistribution sker om det inte finns några relaterade resurser eller om de resurser som definieras av **ExistenceCondition** inte utvärderas till true.
Distributionens varaktighet beror på komplexiteten hos de resurser som ingår i mallen.

Under en utvärderingscykel markeras principdefinitioner med en DeployIfNotExists-effekt som matchar resurser som icke-kompatibla, men ingen åtgärd vidtas för den resursen. Befintliga resurser som inte uppfyller kraven kan åtgärdas med en [reparationsaktivitet](../how-to/remediate-resources.md).

### <a name="deployifnotexists-properties"></a>Egenskaper för DeployIfNotExists

Egenskapen **details** för effekten DeployIfNotExists har alla underegenskaper som definierar relaterade resurser som ska matchas och malldistributionen som ska köras.

- **Typ** [krävs]
  - Anger vilken typ av relaterad resurs som ska matchas.
  - Börjar med att försöka hämta **if** en resurs under om-villkorsresursen och frågar sedan inom samma resursgrupp som om-villkorsresursen. **if**
- **Namn** (valfritt)
  - Anger det exakta namnet på resursen som ska matchas och gör att principen hämtar en specifik resurs i stället för alla resurser av den angivna typen.
  - När villkorsvärdena för **if.field.type** och **then.details.type** _matchar,_ krävs och måste `[field('name')]` **namn** vara .
- **ResourceGroupName** (valfritt)
  - Gör att matchningen av den relaterade resursen kan komma från en annan resursgrupp.
  - Gäller inte om **typen** är en resurs som skulle vara under om-villkorresursen. **if**
  - Standard är **om** villkorsresursens resursgrupp.
  - Om en malldistribution körs distribueras den i resursgruppen för det här värdet.
- **ExistenceScope** (valfritt)
  - Tillåtna värden är _Prenumeration_ och _ResourceGroup_.
  - Anger var den relaterade resursen ska hämtas att matcha från.
  - Gäller inte om **typen** är en resurs som skulle vara under om-villkorresursen. **if**
  - För _ResourceGroup_, skulle begränsa till **om** villkor resurs resursgrupp eller resursgrupp som anges i **ResourceGroupName**.
  - För _Prenumeration_frågar hela prenumerationen för den relaterade resursen.
  - Standard är _ResourceGroup_.
- **ExistensVillkor** (valfritt)
  - Om inget anges uppfyller alla relaterade resurser av **typen** effekten och utlöser inte distributionen.
  - Använder samma språk som principregeln för IF-villkoret, men utvärderas mot varje relaterad resurs individuellt. **if**
  - Om någon matchande relaterad resurs utvärderas till true är effekten nöjd och utlöser inte distributionen.
  - Kan använda [field()] för att kontrollera likvärdighet med värden i **if-villkoret.**
  - Kan till exempel användas för att verifiera att **if** den överordnade resursen (i if-villkoret) finns på samma resursplats som den matchande relaterade resursen.
- **roleDefinitionIds** [krävs]
  - Den här egenskapen måste innehålla en matris med strängar som matchar rollbaserad rollkontrollroll-ID som är tillgängligt för prenumerationen. Mer information finns i [reparation - konfigurera principdefinition](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** (valfritt)
  - Tillåtna värden är _Prenumeration_ och _ResourceGroup_.
  - Anger vilken typ av distribution som ska utlösas. _Prenumeration_ anger en [distribution på prenumerationsnivå](../../../azure-resource-manager/templates/deploy-to-subscription.md), _ResourceGroup_ anger en distribution till en resursgrupp.
  - En _location_ platsegenskap måste anges i _distributionen_ när du använder prenumerationsnivådistributioner.
  - Standard är _ResourceGroup_.
- **Distribution** [krävs]
  - Den här egenskapen bör innehålla den fullständiga `Microsoft.Resources/deployments` malldistributionen eftersom den skickas till PUT API.This property should include the full template deployment as it would be passed to the PUT API. Mer information finns i [REST API FÖR distributioner](/rest/api/resources/deployments).

  > [!NOTE]
  > Alla funktioner i **egenskapen Deployment** utvärderas som komponenter i mallen, inte principen. Undantaget är **egenskapen parameters** som skickar värden från principen till mallen. **Värdet** i det här avsnittet under ett mallparameternamn används för att utföra det här värdet (se exemplet DeployIfNotExists). _fullDbName_

### <a name="deployifnotexists-example"></a>Exempel på DeployIfNotExists

Exempel: Utvärderar SQL Server-databaser för att avgöra om transparentDataEncryption är aktiverat. Om inte, körs en distribution som ska aktiveras.

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

Den här effekten används med `Microsoft.Kubernetes.Data`ett principdefinitionsläge på . *mode* Den används för att skicka gatekeeper v3-åtkomstkontrollregler som definierats med [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint#opa-constraint-framework) till [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) till Kubernetes-kluster på Azure.

> [!NOTE]
> [Azure Policy för Kubernetes](aks-engine.md) är i förhandsversion och stöder endast inbyggda principdefinitioner.

### <a name="enforceopaconstraint-evaluation"></a>EnforceOPAConstraint utvärdering

Åtkomstkontrollanten för öppna principagenten utvärderar alla nya begäranden i klustret i realtid.
Var 15:e minut slutförs en fullständig genomsökning av klustret och resultaten rapporteras till Azure Policy.

### <a name="enforceopaconstraint-properties"></a>Egenskaper för EnforceOPAConstraint

Egenskapen **Details** för effekten EnforceOPAConstraint har de underegenskaper som beskriver gatekeeper v3-behörighetskontrollen.

- **constraintTemplate** [krävs]
  - Villkorsmallen CustomResourceDefinition (CRD) som definierar nya begränsningar. Mallen definierar Rego-logiken, begränsningsschemat och begränsningsparametrarna som skickas via **värden** från Azure Policy.
- **begränsning** [krävs]
  - CRD-implementeringen av villkorsmallen. Använder parametrar **values** som `{{ .Values.<valuename> }}`skickas via värden som . I exemplet nedan skulle `{{ .Values.cpuLimit }}` detta `{{ .Values.memoryLimit }}`vara och .
- **värden** [valfritt]
  - Definierar alla parametrar och värden som ska överföras till begränsningen. Varje värde måste finnas i CRD-mallen Begränsningsmall.

### <a name="enforceopaconstraint-example"></a>FramtvingaOPAConstraint-exempel

Exempel: Gatekeeper v3-behörighetskontrollregel för att ange behållare-CPU- och minnesresursgränser i Kubernetes.

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

Den här effekten används med `Microsoft.ContainerService.Data`ett principdefinitionsläge på . *mode* Det används för att passera Gatekeeper v2-regler för antagningskontroll som definierats med [Rego](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego) to [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) på Azure [Kubernetes Service](../../../aks/intro-kubernetes.md).

> [!IMPORTANT]
> [Azure Policy för Kubernetes](rego-for-aks.md) är i förhandsversion och stöder endast inbyggda principdefinitioner. Inbyggda principer finns i kategorin **Kubernetes.** **Effekten EnforceRegoPolicy** och relaterade **kubernetes-tjänstkategoriprinciper** är _inaktuella_. Använd i stället den uppdaterade [Effekten EnforceOPAConstraint.](#enforceopaconstraint)

### <a name="enforceregopolicy-evaluation"></a>TvingaregoPolicy-utvärdering

Åtkomstkontrollanten för öppna principagenten utvärderar alla nya begäranden i klustret i realtid.
Var femte minut slutförs en fullständig genomsökning av klustret och resultaten rapporteras till Azure Policy.

### <a name="enforceregopolicy-properties"></a>Egenskaper för EnforceRegoPolicy

Egenskapen **Details** för effekten EnforceRegoPolicy har de underegenskaper som beskriver gatekeeper v2-behörighetskontrollen.

- **policyId** [krävs]
  - Ett unikt namn som skickas som en parameter till regeln rego-åtkomstkontroll.
- **policy** [krävs]
  - Anger URI för regeln för rego-behörighetskontroll.
- **policyParameters** [valfritt]
  - Definierar alla parametrar och värden som ska överföras till principen rego.

### <a name="enforceregopolicy-example"></a>Exempel på EnforceRegoPolicy

Exempel: Gatekeeper v2-regel för tillträdeskontroll tillåter endast angivna behållarbilder i AKS.

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

## <a name="layering-policies"></a>Principer för skiktning

En resurs kan påverkas av flera tilldelningar. Dessa tilldelningar kan vara i samma omfattning eller i olika omfattningar. Var och en av dessa tilldelningar kan också ha en annan effekt definierad. Villkoret och effekten för varje princip utvärderas oberoende av varandra. Ett exempel:

- Politik 1
  - Begränsar resursplatsen till "westus"
  - Tilldelad prenumeration A
  - Neka effekt
- Politik 2
  - Begränsar resursplatsen till "eastus"
  - Tilldelad resursgrupp B i prenumeration A
  - Effekt på revision
  
Den här inställningen skulle resultera i följande resultat:

- Alla resurser som redan finns i resursgrupp B i "eastus" är kompatibla med princip 2 och inte överensstämmer med princip 1
- Alla resurser som redan finns i resursgrupp B som inte ingår i "eastus" är inte förenliga med princip 2 och inte överensstämmer med princip 1 om inte i "westus"
- Alla nya resurser i abonnemang A som inte finns i "westus" nekas av policy 1
- Alla nya resurser i prenumeration A och resursgrupp B i "westus" skapas och inte är kompatibla enligt princip 2

Om både politik 1 och politik 2 hade till följd att förneka, ändras situationen till:

- Alla resurser som redan finns i resursgrupp B som inte ingår i "eastus" är inte kompatibla med princip 2
- Alla resurser som redan finns i resursgrupp B som inte ingår i "westus" är inte kompatibla med princip 1
- Alla nya resurser i abonnemang A som inte finns i "westus" nekas av policy 1
- Alla nya resurser i resursgrupp B för prenumeration A nekas

Varje uppgift utvärderas individuellt. Därför finns det inte en möjlighet för en resurs att glida igenom en lucka från skillnader i omfattning. Nettoresultatet av skiktningsprinciper eller överlappning av principer eller principer anses vara **kumulativa mest restriktiva**. Om både princip 1 och 2 har nekats effekt blockeras en resurs av de överlappande och motstridiga principerna. Om du fortfarande behöver skapa resursen i målomfånget granskar du undantagen för varje tilldelning för att validera rätt principer som påverkar rätt scope.

## <a name="next-steps"></a>Nästa steg

- Granska exempel på [Azure Policy-exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](definition-structure.md).
- Förstå hur du [programmässigt skapar principer](../how-to/programmatically-create.md).
- Läs om hur du [hämtar efterlevnadsdata](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).
