---
title: Förstå hur effekterna fungerar
description: Azure principdefinitionen har olika effekter som bestämmer hur kompatibilitet hanteras och rapporteras.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/29/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: ae9c9c5ed8b951760ddac3034c617a13ebe35006
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802651"
---
# <a name="understand-azure-policy-effects"></a>Förstå effekterna av Azure Policy

Varje princip i Azure Policy har en enda effekt. Denna påverkan anger vad som händer när principregeln utvärderas så att de matchar. Effekterna beter sig annorlunda om de är för en ny resurs, en uppdaterad resurs eller en befintlig resurs.

Det finns för närvarande sex effekter som stöds i en definition av principen:

- Lägg till
- Granska
- AuditIfNotExists
- Neka
- DeployIfNotExists
- Disabled

## <a name="order-of-evaluation"></a>Ordningen för utvärdering

Begäranden om att skapa eller uppdatera en resurs via Azure Resource Manager utvärderas av principen först. Principen skapas en lista över alla tilldelningar som tillämpas på resursen och sedan utvärderar resursen mot varje definition. Grupprincip bearbetar flera effekterna innan du skickar begäran till lämplig Resursprovidern. Detta förhindrar onödiga bearbetning av en Resursprovider när en resurs inte uppfyller utformade styrning kontroller av principen.

- **Inaktiverad** kontrolleras först för att fastställa om principregeln bör utvärderas.
- **Lägg till** utvärderas sedan. Lägg sedan till kunde ändra begäran, ändringar av Lägg till kan förhindra en granskningslogg eller neka effekt utlöser.
- **Neka** utvärderas sedan. Neka innan granskningen, dubbla loggning för en oönskad resurs förhindras genom att utvärdera.
- **Granska** utvärderas sedan innan begäran kommer att Resursprovidern.

När Resursprovidern returnerar en framgångskod **AuditIfNotExists** och **DeployIfNotExists** utvärdera för att fastställa om ytterligare kompatibilitet loggning eller åtgärd krävs.

## <a name="disabled"></a>Disabled

Detta är användbart för att testa situationer eller för när principdefinitionen har parameteriserat effekten. Den här flexibiliteten gör det möjligt att inaktivera en enskild tilldelning i stället för att inaktivera alla tilldelningar för den principen.

## <a name="append"></a>Lägg till

Lägg till används för att lägga till fler fält till den begärda resursen under generering och uppdatering. Ett vanligt exempel att lägga till taggar på resurser, till exempel kostnadsställe eller tillåtet att ange IP-adresser för en lagringsresurs.

### <a name="append-evaluation"></a>Lägga till utvärderingen

Lägg till utvärderar innan begäran bearbetas av en Resursprovider under skapandet eller uppdatering av en resurs. Lägg till lägger till fält i resursen när den **om** villkoret för principregeln är uppfyllt. Om Lägg till effekten skulle åsidosätter ett värde i den ursprungliga begäran med ett annat värde kan sedan den fungerar som en nekandeeffekt och avvisar begäran. Om du vill lägga till ett nytt värde i en befintlig matris, använda den **[\*]** version av alias.

När en principdefinition med effekten append körs som en del av en utvärderingscykel, göra den inte ändringar i resurser som redan finns. Istället markeras alla resurser som uppfyller den **om** villkoret som icke-kompatibel.

### <a name="append-properties"></a>Lägg till egenskaper

Lägg till påverkar endast har en **information** matris som krävs. Som **information** är en matris, det kan ta antingen ett enskilt **fält/värde** par eller kopior. Referera till [definitionsstruktur](definition-structure.md#fields) för listan över godkända fält.

### <a name="append-examples"></a>Lägg till exempel

Exempel 1: Enkel **fält/värde** par att lägga till en tagg.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

Exempel 2: Två **fält/värde** par att lägga till en uppsättning taggar.

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

Exempel 3: Enkel **fält/värde** parkopplas med en icke -**[\*]**
[alias](definition-structure.md#aliases) med en matris **värdet** att ange IP-regler för en Storage-konto. När den icke -**[\*]** alias är en matris, effekten lägger till den **värdet** som hela matrisen. Om matrisen redan finns inträffar en neka-händelse från konflikten.

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

Exempel 4: Enkel **fält/värde** parkopplas med en **[\*]** [alias](definition-structure.md#aliases) med en matris **värdet** att ange IP-regler för ett lagringskonto. Med hjälp av den **[\*]** alias, effekten lägger till den **värdet** till en potentiellt befintliga. Om matrisen inte än finns, skapas den.

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

Exempel: Med hjälp av nekandeeffekt.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Granska

Granska används för att skapa en varning-händelse i aktivitetsloggen vid utvärdering av en icke-kompatibel resurs, men slutar inte den begäran.

### <a name="audit-evaluation"></a>Granska utvärdering

Granskning är den sista effekten som kontrolleras av en princip under skapandet eller uppdatering av en resurs. Princip skickar sedan resursen till Resursprovidern. Granskning fungerar på samma sätt för en resursbegäran och en utvärderingscykel för datorprincip. Grupprincip lägger till en `Microsoft.Authorization/policies/audit/action` åtgärden aktivitetsloggen och markerar resursen som icke-kompatibel.

### <a name="audit-properties"></a>Egenskaper för granskning

Granska effekten har inte några ytterligare egenskaper för användning i den **sedan** villkor för principdefinitionen.

### <a name="audit-example"></a>Granska exempel

Exempel: Med effekten granskning.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists aktiverar granskning på resurser som matchar den **om** villkor, men har inte de komponenter som anges i den **information** av den **sedan** villkor.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists utvärdering

AuditIfNotExists körs när en Resursprovider hanterat en skapa eller uppdatera resursbegäran och returnerade statuskoden lyckades. Granskningen uppstår om det finns inga relaterade resurser eller om resurserna som definierats av **ExistenceCondition** inte utvärderas till SANT. Grupprincip lägger till en `Microsoft.Authorization/policies/audit/action` åtgärden till aktiviteten logga på samma sätt som spårningsseffekt. När det utlöses, den resurs som uppfyller den **om** villkoret är den resurs som markeras som icke-kompatibla.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists egenskaper

Den **information** egenskapen om AuditIfNotExists effekterna har alla subegenskaper som definierar de relaterade resurserna så att de matchar.

- **Typ** [krävs]
  - Anger typ av relaterade resurs så att de matchar.
  - Om **details.type** är en resurstyp under den **om** villkoret resurs, principen frågar efter resurser på detta **typ** inom omfånget för den utvärderade resursen. Annars, princip-frågor i samma resursgrupp som den utvärderade resursen.
- **Namn på** (valfritt)
  - Anger det exakta namnet på resursen som ska matcha och gör principen att hämta en specifik resurs i stället för alla resurser av den angivna typen.
  - När villkoret värden för **if.field.type** och **then.details.type** matchar sedan **namn** blir _krävs_ och måste vara `[field('name')]`. Men en [granska](#audit) effekt bör övervägas i stället.
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
  - När villkoret värden för **if.field.type** och **then.details.type** matchar sedan **namn** blir _krävs_ och måste vara `[field('name')]`.
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
- **DeploymentScope** (valfritt)
  - Tillåtna värden är _prenumeration_ och _ResourceGroup_.
  - Anger typ av distribution ska utlösas. _Prenumeration_ anger en [distribution på abonnemangsnivå](../../../azure-resource-manager/deploy-to-subscription.md), _ResourceGroup_ anger en distribution till en resursgrupp.
  - En _plats_ egenskapen måste anges i den _distribution_ när du använder prenumeration på distributioner.
  - Standardvärdet är _ResourceGroup_.
- **Distribution** [krävs]
  - Den här egenskapen ska inkludera fullständig malldistributionen som den skulle skickas till den `Microsoft.Resources/deployments` PLACERA API. Mer information finns i den [distributioner REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Alla funktioner i den **distribution** egenskapen utvärderas som komponenter i mallen, inte på principen. Undantaget är den **parametrar** egenskap som skickar värden från principen för mallen. Den **värdet** i det här avsnittet under en mall för parameternamnet för att utföra det här värdet Skicka (se _fullDbName_ i DeployIfNotExists exempel).

### <a name="deployifnotexists-example"></a>DeployIfNotExists-exempel

Exempel: Utvärderar SQL Server-databaser för att avgöra om transparentDataEncryption är aktiverad. Annars kan du sedan en distribution för att aktivera körs.

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
            "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
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

- Se exempel på [Azure Policy-exempel](../samples/index.md)
- Granska den [Policy-definitionsstruktur](definition-structure.md)
- Förstå hur du [skapa principer programmässigt](../how-to/programmatically-create.md)
- Lär dig hur du [hämta data för kompatibilitetsinställningar](../how-to/getting-compliance-data.md)
- Lär dig hur du [åtgärda icke-kompatibla resurser](../how-to/remediate-resources.md)
- Se över vad en hanteringsgrupp är med sidan om att [organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md)
