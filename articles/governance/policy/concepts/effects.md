---
title: Förstå hur effekter fungerar
description: Azure principdefinitionen har olika effekter som bestämmer hur kompatibilitet hanteras och rapporteras.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 1ac0e70700b4b093fad09b4d10c6bdcf2e06adac
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231521"
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
- [EnforceRegoPolicy](#enforceregopolicy) förhandsgranskningsvyn

## <a name="order-of-evaluation"></a>Ordningen för utvärdering

Begär Anden om att skapa eller uppdatera en resurs via Azure Resource Manager utvärderas av Azure Policy först. Azure Policy skapar en lista med alla tilldelningar som gäller för resursen och utvärderar sedan resursen mot varje definition. Azure Policy bearbetar flera av effekterna innan du skickar begäran till rätt resurs leverantör. Detta förhindrar onödig bearbetning av en resurs leverantör när en resurs inte uppfyller de design kontroller som Azure Policy.

- **Inaktiverad** kontrolleras först för att fastställa om principregeln bör utvärderas.
- **Lägg till** utvärderas sedan. Lägg sedan till kunde ändra begäran, ändringar av Lägg till kan förhindra en granskningslogg eller neka effekt utlöser.
- **Neka** utvärderas sedan. Neka innan granskningen, dubbla loggning för en oönskad resurs förhindras genom att utvärdera.
- **Granska** utvärderas sedan innan begäran kommer att Resursprovidern.

När Resursprovidern returnerar en framgångskod **AuditIfNotExists** och **DeployIfNotExists** utvärdera för att fastställa om ytterligare kompatibilitet loggning eller åtgärd krävs.

Det finns för närvarande ingen utvärderings ordning för **EnforceRegoPolicy** -påverkan.

## <a name="disabled"></a>Inaktiverad

Detta är användbart för att testa situationer eller för när principdefinitionen har parameteriserat effekten. Den här flexibiliteten gör det möjligt att inaktivera en enskild tilldelning i stället för att inaktivera alla tilldelningar för den principen.

## <a name="append"></a>Lägg till

Lägg till används för att lägga till fler fält till den begärda resursen under generering och uppdatering. Ett vanligt exempel att lägga till taggar på resurser, till exempel kostnadsställe eller tillåtet att ange IP-adresser för en lagringsresurs.

### <a name="append-evaluation"></a>Lägga till utvärderingen

Lägg till utvärderar innan begäran bearbetas av en Resursprovider under skapandet eller uppdatering av en resurs. Lägg till lägger till fält i resursen när den **om** villkoret för principregeln är uppfyllt. Om Lägg till effekten skulle åsidosätter ett värde i den ursprungliga begäran med ett annat värde kan sedan den fungerar som en nekandeeffekt och avvisar begäran. Om du vill lägga till ett nytt värde i en befintlig matris använder du **[\*]** -versionen av aliaset.

När en principdefinition med effekten append körs som en del av en utvärderingscykel, göra den inte ändringar i resurser som redan finns. Istället markeras alla resurser som uppfyller den **om** villkoret som icke-kompatibel.

### <a name="append-properties"></a>Lägg till egenskaper

Lägg till påverkar endast har en **information** matris som krävs. Som **information** är en matris, det kan ta antingen ett enskilt **fält/värde** par eller kopior. Referera till [definitionsstruktur](definition-structure.md#fields) för listan över godkända fält.

### <a name="append-examples"></a>Lägg till exempel

Exempel 1: Enkelt **fält/värde-** par för att lägga till en tagg.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

Exempel 2: Två **fält/värde-** par för att lägga till en uppsättning taggar.

```json
"then": {
    "effect": "append",
    "details": [{
            "field": "tags.myTag",
            "value": "myTagValue"
        },
        {
            "field": "tags.myOtherTag",
            "value": "myOtherTagValue"
        }
    ]
}
```

Exempel 3: Ett **fält/värde** -par med ett icke **-\*[]** - [alias](definition-structure.md#aliases) med ett mat ris **värde** som anger IP-regler för ett lagrings konto. När ett icke- **[\*]** -alias är en matris lägger effekterna till **värdet** som hela matrisen. Om matrisen redan finns inträffar en Deny-händelse från konflikten.

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

Exempel 4: Ett **fält/värde** -par med **ett\*[]** - [alias](definition-structure.md#aliases) med ett mat ris **värde** som anger IP-regler för ett lagrings konto. Genom att använda **[\*]** -aliaset lägger du till **värdet** i en befintlig matris som kan användas. Om matrisen ännu inte finns kommer den att skapas.

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

## <a name="deny"></a>Neka

Neka används för att förhindra att en resursbegäran som matchar inte definierad standarder genom en principdefinition och misslyckas begäran.

### <a name="deny-evaluation"></a>Neka utvärdering

När skapar eller uppdaterar en matchande resurs neka förhindrar att begäran innan den skickas till Resursprovidern. Begäran returneras som en `403 (Forbidden)`. I portalen kan förbjudet visas som en status för distributionen förhindrades av principtilldelningen.

Under utvärdering av befintliga resurser markeras resurser som matchar en neka-principdefinition som icke-kompatibla.

### <a name="deny-properties"></a>Neka egenskaper

Nekandeeffekt har inte några ytterligare egenskaper för användning i den **sedan** villkor för principdefinitionen.

### <a name="deny-example"></a>Neka exempel

Exempel: Med hjälp av neka-påverkan.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Granska

Granska används för att skapa en varning-händelse i aktivitetsloggen vid utvärdering av en icke-kompatibel resurs, men slutar inte den begäran.

### <a name="audit-evaluation"></a>Granska utvärdering

Audit är den senaste effekterna som kontrol leras av Azure Policy när en resurs skapas eller uppdateras. Azure Policy skickar sedan resursen till resurs leverantören. Granskning fungerar på samma sätt för en resursbegäran och en utvärderingscykel för datorprincip. Azure policy lägger till `Microsoft.Authorization/policies/audit/action` en åtgärd i aktivitets loggen och markerar resursen som icke-kompatibel.

### <a name="audit-properties"></a>Egenskaper för granskning

Granska effekten har inte några ytterligare egenskaper för användning i den **sedan** villkor för principdefinitionen.

### <a name="audit-example"></a>Granska exempel

Exempel: Använda gransknings effekterna.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists aktiverar granskning på resurser som matchar den **om** villkor, men har inte de komponenter som anges i den **information** av den **sedan** villkor.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists utvärdering

AuditIfNotExists körs när en Resursprovider hanterat en skapa eller uppdatera resursbegäran och returnerade statuskoden lyckades. Granskningen uppstår om det finns inga relaterade resurser eller om resurserna som definierats av **ExistenceCondition** inte utvärderas till SANT. Azure policy lägger till `Microsoft.Authorization/policies/audit/action` en åtgärd i aktivitets loggen på samma sätt som gransknings resultatet. När det utlöses, den resurs som uppfyller den **om** villkoret är den resurs som markeras som icke-kompatibla.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists egenskaper

Den **information** egenskapen om AuditIfNotExists effekterna har alla subegenskaper som definierar de relaterade resurserna så att de matchar.

- **Typ** [krävs]
  - Anger typ av relaterade resurs så att de matchar.
  - Om **information. Type** är en resurs typ under **IF** -villkor-resursen, frågar principen efter resurser av den här **typen** inom omfånget för den utvärderade resursen. I annat fall är princip frågorna inom samma resurs grupp som den utvärderade resursen.
- **Namn på** (valfritt)
  - Anger det exakta namnet på resursen som ska matcha och gör principen att hämta en specifik resurs i stället för alla resurser av den angivna typen.
  - När villkors värden för **IF. Field. Type** och **then.** details. Type match, blir **namnet** _obligatoriskt_ och måste `[field('name')]`vara. En gransknings [](#audit) funktion bör dock beaktas i stället.
- **ResourceGroupName** (valfritt)
  - Tillåter matchningen av relaterade resursen komma från en annan resursgrupp.
  - Gäller inte om **typ** är en resurs som är under den **om** villkoret resurs.
  - Standardvärdet är den **om** villkoret resursens resursgrupp.
- **ExistenceScope** (valfritt)
  - Tillåtna värden är _prenumeration_ och _ResourceGroup_.
  - Anger vilket scope för vart du ska hämta relaterade resursen som ska matcha från.
  - Gäller inte om **typ** är en resurs som är under den **om** villkoret resurs.
  - För _ResourceGroup_, vilket begränsar till den **om** resursgrupp för villkoret resursen eller resursgruppen som anges i **ResourceGroupName**.
  - För _prenumeration_, frågar hela prenumerationen för den tillhörande resursen.
  - Standardvärdet är _ResourceGroup_.
- **ExistenceCondition** (valfritt)
  - Om inte anges relaterade resurs av **typ** uppfyller effekten och utlöses inte granskningen.
  - Använder samma språk som principregeln för den **om** villkoret, men ska utvärderas mot alla relaterade resurser individuellt.
  - Om alla matchande relaterade resurser utvärderas till SANT, effekten har uppfyllts och utlöses inte granskningen.
  - Kan använda [field()] för att kontrollera överensstämmelse med värden i den **om** villkor.
  - Exempelvis kan användas för att kontrollera att den överordnade resursen (i den **om** villkor) är i samma resursplats som den matchande relaterad resursen.

### <a name="auditifnotexists-example"></a>AuditIfNotExists exempel

Exempel: Utvärderar Virtual Machines för att avgöra om tillägget för program mot skadlig kod finns, granskas när de saknas.

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

Liknar AuditIfNotExists, DeployIfNotExists körs en för malldistribution när villkoret uppfylls.

> [!NOTE]
> [Kapslade mallar](../../../azure-resource-manager/resource-group-linked-templates.md#nested-template) stöds med **deployIfNotExists**, men [länkade mallar](../../../azure-resource-manager/resource-group-linked-templates.md) stöds inte för närvarande.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists-utvärdering

DeployIfNotExists körs när en Resursprovider hanterat en skapa eller uppdatera resursbegäran och returnerade statuskoden lyckades. En för malldistributionen ska ske om det finns inga relaterade resurser eller om resurserna som definierats av **ExistenceCondition** inte utvärderas till SANT.

Under en utvärderingscykel principdefinitioner med effekten DeployIfNotExists som matchar resurser markeras som icke-kompatibel, men ingen åtgärd utförs på den här resursen.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists-egenskaper

Den **information** egenskapen för DeployIfNotExists effekterna har alla subegenskaper som definierar de relaterade resurserna till matchning och malldistribution för att köra.

- **Typ** [krävs]
  - Anger typ av relaterade resurs så att de matchar.
  - Startar genom att hämta en resurs under den **om** villkor resurs och frågor inom samma resursgrupp som den **om** villkoret resurs.
- **Namn på** (valfritt)
  - Anger det exakta namnet på resursen som ska matcha och gör principen att hämta en specifik resurs i stället för alla resurser av den angivna typen.
  - När villkors värden för **IF. Field. Type** och **then.** details. Type match, blir **namnet** _obligatoriskt_ och måste `[field('name')]`vara.
- **ResourceGroupName** (valfritt)
  - Tillåter matchningen av relaterade resursen komma från en annan resursgrupp.
  - Gäller inte om **typ** är en resurs som är under den **om** villkoret resurs.
  - Standardvärdet är den **om** villkoret resursens resursgrupp.
  - Om en malldistributionen körs har den distribuerats i resursgruppen för det här värdet.
- **ExistenceScope** (valfritt)
  - Tillåtna värden är _prenumeration_ och _ResourceGroup_.
  - Anger vilket scope för vart du ska hämta relaterade resursen som ska matcha från.
  - Gäller inte om **typ** är en resurs som är under den **om** villkoret resurs.
  - För _ResourceGroup_, vilket begränsar till den **om** resursgrupp för villkoret resursen eller resursgruppen som anges i **ResourceGroupName**.
  - För _prenumeration_, frågar hela prenumerationen för den tillhörande resursen.
  - Standardvärdet är _ResourceGroup_.
- **ExistenceCondition** (valfritt)
  - Om inte anges relaterade resurs av **typ** uppfyller effekten och utlöses inte distributionen.
  - Använder samma språk som principregeln för den **om** villkoret, men ska utvärderas mot alla relaterade resurser individuellt.
  - Om alla matchande relaterade resurser utvärderas till SANT, effekten har uppfyllts och utlöses inte distributionen.
  - Kan använda [field()] för att kontrollera överensstämmelse med värden i den **om** villkor.
  - Exempelvis kan användas för att kontrollera att den överordnade resursen (i den **om** villkor) är i samma resursplats som den matchande relaterad resursen.
- **roleDefinitionIds** [krävs]
  - Den här egenskapen måste innehålla en matris med strängar som matchar rollbaserad åtkomstkontroll roll-ID nås av prenumerationen. Mer information finns i [reparation – konfigurera principdefinitionen](../how-to/remediate-resources.md#configure-policy-definition).
- **DeploymentScope** valfritt
  - Tillåtna värden är _prenumeration_ och _ResourceGroup_.
  - Anger vilken typ av distribution som ska utlösas. _Prenumerationen_ anger en [distribution på prenumerations nivå](../../../azure-resource-manager/deploy-to-subscription.md), _ResourceGroup_ anger en distribution till en resurs grupp.
  - En _plats_ egenskap måste anges i _distributionen_ när du använder distributioner på prenumerations nivå.
  - Standardvärdet är _ResourceGroup_.
- **Distribution** [krävs]
  - Den här egenskapen ska inkludera fullständig malldistributionen som den skulle skickas till den `Microsoft.Resources/deployments` PLACERA API. Mer information finns i den [distributioner REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Alla funktioner i den **distribution** egenskapen utvärderas som komponenter i mallen, inte på principen. Undantaget är den **parametrar** egenskap som skickar värden från principen för mallen. Den **värdet** i det här avsnittet under en mall för parameternamnet för att utföra det här värdet Skicka (se _fullDbName_ i DeployIfNotExists exempel).

### <a name="deployifnotexists-example"></a>DeployIfNotExists-exempel

Exempel: Utvärderar SQL Server databaser för att avgöra om transparentDataEncryption har Aktiver ATS. Annars körs en distribution som ska aktive ras.

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

Den här inställningen används med ett princip definitions *läge* för `Microsoft.ContainerService.Data`. Den används för att skicka regler för åtkomst kontroll som definierats med [Rego](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego) för att [Öppna princip agenten](https://www.openpolicyagent.org/) (OPA) på [Azure Kubernetes-tjänsten](../../../aks/intro-kubernetes.md).

> [!NOTE]
> [Azure policy för Kubernetes](rego-for-aks.md) finns i en offentlig för hands version och stöder bara inbyggda princip definitioner.

### <a name="enforceregopolicy-evaluation"></a>EnforceRegoPolicy-utvärdering

Den öppna princip agentens åtkomst kontroll utvärderar alla nya begär anden i klustret i real tid.
Var 5: e minut slutförs en fullständig genomsökning av klustret och resultaten rapporteras till Azure Policy.

### <a name="enforceregopolicy-properties"></a>Egenskaper för EnforceRegoPolicy

Egenskapen **information** för EnforceRegoPolicy-effekter har de subegenskaper som beskriver reglerna för Rego-åtkomstkontroll.

- **policyId** kunna
  - Ett unikt namn som skickas som en parameter till Rego-åtkomstkontroll.
- **princip** kunna
  - Anger URI för Rego-åtkomstkontroll.
- **policyParameters** valfritt
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

## <a name="layering-policies"></a>Skikta principer

En resurs kan påverkas av flera tilldelningar. Dessa tilldelningar kan vara i samma definitionsområde eller i olika omfång. Var och en av de här tilldelningarna troligtvis även har en annan effekt som definierats. Villkor och effekt för varje princip utvärderas oberoende av varandra. Exempel:

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

Varje tilldelning utvärderas individuellt. Därför det är inte en möjlighet för en resurs för försening via ett uppehåll av skillnader i omfånget. Resultatet av lagren principer eller princip överlappning anses vara **kumulativa mest restriktiva**. Till exempel om båda princip 1 och 2 hade en nekandeeffekt, blockeras en resurs av överlappande och motstridiga principer. Om du fortfarande behöver resursen ska påverkar skapas i målområdet, granska undantag vid varje uppgift att verifiera rätt principer rätt scope.

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](definition-structure.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/getting-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).