---
title: Förstå effekterna av Azure Policy
description: Azure principdefinitionen har olika effekter som bestämmer hur kompatibilitet hanteras och rapporteras.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 54562401c830232d0a4bf90405cc5a2dbedcd8bc
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055976"
---
# <a name="understand-policy-effects"></a>Förstå effekterna av princip

Varje principdefinitionen i Azure Policy har en enda effekt som anger vad som händer under genomsökning när den **om** segment för principregeln utvärderas för att matcha resursen som genomsöks. Effekterna kan också fungera annorlunda om de är för en ny resurs, en uppdaterad resurs eller en befintlig resurs.

Det finns för närvarande fem effekter som stöds i en definition av principen:

- Lägg till
- Granska
- AuditIfNotExists
- Neka
- DeployIfNotExists

## <a name="order-of-evaluation"></a>Ordningen för utvärdering

När en begäran om att skapa eller uppdatera en resurs via Azure Resource Manager görs, bearbetar Grupprincip flera effekterna innan du lämnar över begäran till lämplig Resursprovidern.
Detta förhindrar onödiga bearbetning av en Resursprovider när en resurs inte uppfyller utformade styrning kontroller av principen. Principen skapas en lista över alla principdefinitioner som tilldelats av en princip eller initiativtilldelning, som tillämpas av omfång (minus undantag) till resursen och förbereder för att utvärdera resurs mot varje definition.

- **Lägg till** utvärderas först. Lägg sedan till kunde ändra begäran, ändringar av Lägg till kan förhindra en granskningslogg eller neka effekt utlöser.
- **Neka** utvärderas sedan. Neka innan granskningen, dubbla loggning för en oönskad resurs förhindras genom att utvärdera.
- **Granska** utvärderas sedan innan begäran kommer att Resursprovidern.

När begäran har angetts för Resursprovidern och Resursprovidern returnerar statuskoden lyckades **AuditIfNotExists** och **DeployIfNotExists** utvärderas för att avgöra om uppföljning efterlevnad loggning eller åtgärd krävs.

## <a name="append"></a>Lägg till

Lägg till används för att lägga till fler fält till den begärda resursen under generering och uppdatering. Det kan vara användbart för att lägga till taggar på resurser, till exempel kostnadsställe eller tillåtet att ange IP-adresser för en lagringsresurs.

### <a name="append-evaluation"></a>Lägga till utvärderingen

Som tidigare nämnts kan du lägga till utvärderar innan begäran komma bearbetas av en Resursprovider under skapandet eller uppdatering av en resurs. Lägg till lägger till fält i resursen när den **om** villkoret för principregeln är uppfyllt. Om Lägg till effekten skulle åsidosätter ett värde i den ursprungliga begäran med ett annat värde kan sedan den fungerar som en nekandeeffekt och avvisa begäran.

När en principdefinition med effekten append körs som en del av en utvärderingscykel, den inte göra ändringar i resurser som redan finns. Istället markeras alla resurser som uppfyller den **om** villkoret som icke-kompatibel.

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

Exempel 2: Flera **fält/värde** par att lägga till en uppsättning taggar.

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

Exempel 3: Enkel **fält/värde** parkopplas med en [alias](definition-structure.md#aliases) med en matris **värdet** att ange IP-regler för ett lagringskonto.

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

## <a name="deny"></a>Neka

Neka används för att förhindra att en resursbegäran som matchar inte önskad standarder genom en principdefinition och misslyckas begäran.

### <a name="deny-evaluation"></a>Neka utvärdering

När skapar eller uppdaterar en resurs, neka hindrar begäran innan de skickas till Resursprovidern. Begäran returneras som ett 403 (förbjudet). I portalen kan förbjudet visas som en status för distributionen som kunde inte på grund av principtilldelningen.

Under en utvärderingscykel principdefinitioner med en nekandeeffekt som matchar resurser markeras som icke-kompatibel, men ingen åtgärd utförs på den här resursen.

### <a name="deny-properties"></a>Neka egenskaper

Nekandeeffekt har inte några ytterligare egenskaper för användning i den **sedan** villkor för principdefinitionen.

### <a name="deny-example"></a>Neka exempel

Exempel: Med nekandeeffekt.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Granska

Spårningsseffekt används för att skapa en varning-händelse i aktivitetsloggen när en icke-kompatibel resurs utvärderas, men det hindrar inte begäran.

### <a name="audit-evaluation"></a>Granska utvärdering

Granska effekten är sist att köras under skapandet eller uppdatering av en resurs innan resursen som skickas till Resursprovidern. Granskning fungerar på samma sätt för en resursbegäran och en utvärderingscykel för datorprincip och kör en `Microsoft.Authorization/policies/audit/action` åtgärden till aktivitetsloggen. I båda fallen markeras resursen som icke-kompatibla.

### <a name="audit-properties"></a>Egenskaper för granskning

Granska effekten inte har några ytterligare egenskaper för användning i den **sedan** villkor för principdefinitionen.

### <a name="audit-example"></a>Granska exempel

Exempel: Med effekten granskning.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists aktiverar granskning på en resurs som matchar den **om** villkor, men har inte de komponenter som anges i den **information** av den **sedan** villkor.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists utvärdering

AuditIfNotExists körs när en Resursprovider hanterat en skapa eller uppdatera begäran till en resurs och returnerade statuskoden lyckades. Effekten utlöses om det finns inga relaterade resurser eller om resurserna som definierats av **ExistenceCondition** inte utvärderas till SANT. När effekten utlöses en `Microsoft.Authorization/policies/audit/action` körs på samma sätt som spårningsseffekt åtgärden till aktivitetsloggen. När det utlöses, den resurs som uppfyller den **om** villkoret är den resurs som markeras som icke-kompatibla.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists egenskaper

Den **information** egenskapen om AuditIfNotExists effekterna har alla subegenskaper som definierar de relaterade resurserna så att de matchar.

- **Typ** [krävs]
  - Anger typ av relaterade resurs så att de matchar.
  - Startar genom att hämta en resurs under den **om** villkor resurs och frågor inom samma resursgrupp som den **om** villkoret resurs.
- **Namn på** (valfritt)
  - Anger det exakta namnet på resursen som ska matcha och gör principen att hämta en specifik resurs i stället för alla resurser av den angivna typen.
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
  - Om inte anges relaterade resurs av **typ** uppfyller effekten och inte utlöser granskningen.
  - Använder samma språk som principregeln för den **om** villkoret, men ska utvärderas mot alla relaterade resurser individuellt.
  - Om alla matchande relaterade resurser utvärderas till SANT, effekten har uppfyllts och utlöser inte granskningen.
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

DeployIfNotExists körs även när en Resursprovider hanterat skapa eller uppdatera begäran till en resurs och returnerade statuskoden lyckades. Effekten utlöses om det finns inga relaterade resurser eller om resurserna som definierats av **ExistenceCondition** inte utvärderas till SANT. När effekten utlöses, utförs en för malldistribution.

Under en utvärderingscykel principdefinitioner med effekten DeployIfNotExists som matchar resurser markeras som icke-kompatibel, men ingen åtgärd utförs på den här resursen.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists-egenskaper

Den **information** egenskapen för DeployIfNotExists effekterna har alla subegenskaper som definierar de relaterade resurserna till matchning och malldistribution för att köra.

- **Typ** [krävs]
  - Anger typ av relaterade resurs så att de matchar.
  - Startar genom att hämta en resurs under den **om** villkor resurs och frågor inom samma resursgrupp som den **om** villkoret resurs.
- **Namn på** (valfritt)
  - Anger det exakta namnet på resursen som ska matcha och gör principen att hämta en specifik resurs i stället för alla resurser av den angivna typen.
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
  - Om inte anges relaterade resurs av **typ** uppfyller effekten och inte utlöser distributionen.
  - Använder samma språk som principregeln för den **om** villkoret, men ska utvärderas mot alla relaterade resurser individuellt.
  - Om alla matchande relaterade resurser utvärderas till SANT, effekten har uppfyllts och utlöser inte distributionen.
  - Kan använda [field()] för att kontrollera överensstämmelse med värden i den **om** villkor.
  - Exempelvis kan användas för att kontrollera att den överordnade resursen (i den **om** villkor) är i samma resursplats som den matchande relaterad resursen.
- **roleDefinitionIds** [krävs]
  - Den här egenskapen måste innehålla en matris med strängar som matchar rollbaserad åtkomstkontroll roll-ID nås av prenumerationen. Mer information finns i [reparation – konfigurera principdefinitionen](../how-to/remediate-resources.md#configure-policy-definition).
- **Distribution** [krävs]
  - Den här egenskapen ska innehålla den fullständiga malldistributionen som den skulle skickas till den `Microsoft.Resources/deployments` PLACERA API. Mer information finns i den [distributioner REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Alla funktioner i den **distribution** egenskapen utvärderas som komponenter i mallen, inte på principen. Undantaget är den **parametrar** egenskap som skickar värden från principen för mallen. Den **värdet** i det här avsnittet under en mall för parameternamnet för att utföra det här värdet Skicka (se _fullDbName_ i DeployIfNotExists exempel).

### <a name="deployifnotexists-example"></a>DeployIfNotExists-exempel

Exempel: Utvärderar SQL Server-databaser för att avgöra om transparentDataEncryption är aktiverad. Annars kan du sedan en distribution så att den körs.

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
                    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

En resurs kan påverkas av flera tilldelningar. Dessa tilldelningar kan vara i samma definitionsområde (specifik resurs, resursgrupp, prenumeration eller hanteringsgrupp) eller i olika omfång. Var och en av de här tilldelningarna troligtvis även har en annan effekt som definierats. Oavsett utvärderas oberoende villkor och effekt för varje policy för (direkt eller som en del av ett initiativ). Till exempel om principen 1 innehåller ett villkor som begränsar resursplats för prenumeration A bara skapas i westus om du med nekandeeffekt och princip 2 har vara ett villkor som begränsar resursplats för resursgruppen B (som finns i prenumeration A) till bara båda skapas i ”eastus” med spårningsseffekt är tilldelat, resulterande resultatet skulle vara::

- Alla resurser redan i resursgruppen B i ”eastus” är kompatibla med principen 2, men markeras som icke-kompatibla principen 1.
- Alla resurser redan i resursgruppen B inte i ”eastus” kommer att markeras som icke-kompatibla principen 2 och skulle också markeras inte kompatibla med principen 1 om det inte ”westus”.
- En ny resurs i prenumeration A skulle inte i 'westus' avvisas av Grupprincip 1.
- En ny resurs i prenumeration A / resursgrupp B i 'westus' skulle markeras som icke-kompatibla på princip 2, men skulle skapas (kompatibelt med 1 och 2 är gransknings- och inte neka).

Om både 1 och 2 i hade kraft av neka, situationen skulle ändras till:

- Alla resurser redan i resursgruppen B inte i ”eastus” kommer att markeras som icke-kompatibla principen 2.
- Alla resurser redan i resursgruppen B inte i ”westus' kommer att markeras som icke-kompatibla principen 1.
- En ny resurs i prenumeration A skulle inte i 'westus' avvisas av Grupprincip 1.
- En ny resurs i prenumeration A / B-resursgrupp skulle avvisas (eftersom dess plats kan aldrig uppfyller både 1 och 2).

Eftersom varje tilldelning utvärderas individuellt och det finns inte en möjlighet för en resurs för försening via en lucka på grund av skillnader i omfånget. Resultatet av lagren principer eller princip överlappning anses därför vara **kumulativa mest restriktiva**. En resurs som du vill skapa kan med andra ord att blockeras på grund av överlappande och motstridiga principer, till exempel i exemplet ovan om både 1 och 2 hade en nekandeeffekt. Om du fortfarande behöver resursen som ska skapas i målområdet granska undantag vid varje uppgift att säkerställa att rätt principer påverkar rätt scope.

## <a name="next-steps"></a>Nästa steg

- Se exempel på [Azure Policy-exempel](../samples/index.md)
- Granska den [Policy-definitionsstruktur](definition-structure.md)
- Förstå hur du [skapa principer programmässigt](../how-to/programmatically-create.md)
- Lär dig hur du [hämta data för kompatibilitetsinställningar](../how-to/getting-compliance-data.md)
- Upptäck hur du [åtgärda icke-kompatibla resurser](../how-to/remediate-resources.md)
- Se över vad en hanteringsgrupp är med sidan om att [organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md)