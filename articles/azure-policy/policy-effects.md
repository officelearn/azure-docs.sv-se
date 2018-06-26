---
title: Förstå Azure princip effekter
description: Azure principdefinitionen har olika effekter som bestämmer hur kompatibilitet hanteras och rapporteras.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 1566cf2b61749121c4eaff5a32b0a940f3341f7e
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751786"
---
# <a name="understanding-policy-effects"></a>Förstå effekterna av principer

Varje principdefinitionen i Azure-principen har en enda effekt som anger vad som händer vid skanningen när den **om** segment för principregeln utvärderas för att matcha resursen som genomsöks. Effekterna kan också fungera annorlunda om de är för en ny resurs, en uppdaterad resurs eller en befintlig resurs.

Det finns fem effekter som stöds i en principdefinition:

- Lägg till
- Granska
- AuditIfNotExists
- Neka
- DeployIfNotExists

## <a name="order-of-evaluation"></a>Ordningen för utvärderingen

När en begäran att skapa eller uppdatera en resurs via Azure Resource Manager, bearbetar flera effekterna innan sedan leverera begäran till lämplig Resursprovidern princip.
Gör det förhindrar onödig genom en Resursleverantör när en resurs inte uppfyller utformad styrning kontroller av principen. Principen skapar en lista över alla principdefinitioner som tilldelats av en princip eller initiativ tilldelning som tillämpas av omfång (minus undantag) till resursen och förbereder för att utvärdera resursen mot varje definition.

- **Lägg till** utvärderas först. Lägg sedan till kunde ändra begäran, ändringar av bifoga kan förhindra en granskning eller neka gälla från utlösa.
- **Neka** utvärderas sedan. Neka innan granskning, dubbla loggning för en resurs som områdena förhindras genom att utvärdera.
- **Granska** utvärderas sedan innan begäran ska Resursprovidern.

När begäran har angetts för Resursprovidern och Resursprovidern returnerar statuskod lyckade **AuditIfNotExists** och **DeployIfNotExists** utvärderas för att avgöra om uppföljning kompatibilitet loggning eller åtgärd krävs.

## <a name="append"></a>Lägg till

Lägg till används för att lägga till ytterligare fält under Skapa eller uppdatera den begärda resursen. Det kan vara användbart för att lägga till taggar på resurser, till exempel costCenter eller att ange tillåtna IP-adresser för en lagringsresurs.

### <a name="append-evaluation"></a>Lägg till utvärdering

Som tidigare nämnts kan du lägga till utvärderar innan begäran komma bearbetas av en Resursleverantör under skapande eller uppdatering av en resurs. Lägg till fält lägger till resursen när den **om** för principregeln är uppfyllt. Om effekten append skulle åsidosätter ett värde i den ursprungliga begäranden med ett annat värde, sedan den fungerar som en neka-effekt och avvisa begäran.

När en principdefinition med effekten append körs som en del av en utvärderingscykel, den inte göra ändringar i resurser som redan finns. I stället markeras alla resurser som uppfyller den **om** villkor som icke-kompatibla.

### <a name="append-properties"></a>Lägg till egenskaper

Har bara betydelse Lägg till en **information** matris som krävs. Som **information** är en matris kan det ta antingen en enskild **fält/värde-** par eller multiplar. Referera till [principdefinitionen](policy-definition.md#fields) för listan över godkända fält.

### <a name="append-examples"></a>Lägg till exempel

Exempel 1: Enkel **fält/värde-** par om du vill lägga till en tagg.

```json
"then": {
    "effect": "append",
    "details": [{
        "field": "tags.myTag",
        "value": "myTagValue"
    }]
}
```

Exempel 2: Flera **fält/värde-** par att lägga till en uppsättning taggar.

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

Exempel 3: Enkel **fält/värde-** para ihop med en [alias](policy-definition.md#aliases) med en matris **värdet** att ange IP-regler för ett lagringskonto.

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

Neka används för att förhindra att en resursbegäran som inte överensstämmer med önskade standard via en principdefinition och misslyckas begäran.

### <a name="deny-evaluation"></a>Neka utvärdering

När skapar eller uppdaterar en resurs neka hindrar begäran innan som skickas till Resursprovidern. Begäran returneras som ett 403 (förbjuden). Förbjuden kan visas som en status för distributionen som kunde inte på grund av principtilldelningen i portalen.

Under en utvärderingscykel principdefinitioner med neka effekt som matchar resurser är markerad som icke-kompatibla, men inga åtgärder utförs på den här resursen.

### <a name="deny-properties"></a>Neka egenskaper

Neka effekten inte har några ytterligare egenskaper för användning i den **sedan** villkoret för principdefinitionen.

### <a name="deny-example"></a>Neka-exempel

Exempel: Med neka-effekt.

```json
"then": {
    "effect": "deny"
}
```

## <a name="audit"></a>Granska

Granska effekten används för att skapa en varning-händelse i händelseloggen när en icke-kompatibla resurs utvärderas, men stoppar inte begäran.

### <a name="audit-evaluation"></a>Granska utvärdering

Granska effekten är sist att köras under skapandet eller uppdatering av en resurs innan resursen skickas till Resursprovidern. Granska fungerar på samma sätt för en resursbegäran och ett utvärderingscykel och kör en `Microsoft.Authorization/policies/audit/action` åtgärden aktivitetsloggen. I båda fallen är resursen markerad som icke-kompatibla.

### <a name="audit-properties"></a>Egenskaper för granskning

Granska effekten inte har några ytterligare egenskaper för användning i den **sedan** villkoret för principdefinitionen.

### <a name="audit-example"></a>Gransknings-exempel

Exempel: Använda gälla för granskning.

```json
"then": {
    "effect": "audit"
}
```

## <a name="auditifnotexists"></a>AuditIfNotExists

AuditIfNotExists aktiverar granskning på en resurs som matchar den **om** villkor, men har inte de komponenter som anges i den **information** av den **sedan** villkor.

### <a name="auditifnotexists-evaluation"></a>AuditIfNotExists utvärdering

AuditIfNotExists körs när en Resursleverantör hanterades en skapa eller uppdatera begäran till en resurs och returnerade en statuskod för lyckade. Effekten utlöses om det finns inga relaterade resurser eller om resurserna som definieras av **ExistenceCondition** inte utvärderas till SANT. När effekten utlöses en `Microsoft.Authorization/policies/audit/action` till aktivitetsloggen utförs på samma sätt som gälla för granskning. När den utlöses den resurs som uppfyller den **om** villkoret är den resurs som är markerad som icke-kompatibla.

### <a name="auditifnotexists-properties"></a>AuditIfNotExists egenskaper

Den **information** egenskapen AuditIfNotExists effekter har alla subegenskaper som definierar relaterade resurser för att matcha.

- **Typen** [krävs]
  - Anger typ av relaterade resurs så att den matchar.
  - Startar genom att hämta en resurs under den **om** villkoret resurs och frågor i samma resursgrupp som det **om** condition-resurs.
- **Namnet** (valfritt)
  - Anger det exakta namnet av resursen ska matcha och gör att principen att hämta en specifik resurs i stället för alla resurser för den angivna typen.
- **ResourceGroupName** (valfritt)
  - Tillåter matchning av relaterade resursen ska hämtas från en annan resursgrupp.
  - Gäller inte om **typen** är en resurs som är under den **om** condition-resurs.
  - Standardvärdet är den **om** villkor resursens resursgruppen.
- **ExistenceScope** (valfritt)
  - Tillåtna värden är _prenumeration_ och _ResourceGroup_.
  - Anger det scope där att hämta relaterade resursen ska matcha från.
  - Gäller inte om **typen** är en resurs som är under den **om** condition-resurs.
  - För _ResourceGroup_, skulle begränsa till den **om** villkoret resurs, resursgrupp eller resursgruppen som anges i **ResourceGroupName**.
  - För _prenumeration_, frågar hela prenumerationen för relaterad resurs.
  - Standardvärdet är _ResourceGroup_.
- **ExistenceCondition** (valfritt)
  - Om inget annat anges, alla relaterade resurs av **typen** uppfyller effekt och inte utlöser revisionen.
  - Använder samma språk som principregel för den **om** villkor, men utvärderas mot varje relaterad resurs individuellt.
  - Om alla matchande relaterade resurser evalueras till true, effekten är nöjd och utlöser inte revisionen.
  - Kan använda [field()] för att kontrollera överensstämmelse med värden i den **om** villkor.
  - Till exempel kan användas för att kontrollera att den överordnade resursen (i det **om** villkor) finns på samma resursplats som matchande relaterad resurs.

### <a name="auditifnotexists-example"></a>AuditIfNotExists-exempel

Exempel: Utvärderar virtuella datorer för att avgöra om filnamnstillägget program mot skadlig kod finns sedan granskningar när saknas.

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

Liknar AuditIfNotExists, DeployIfNotExists kör en malldistribution när villkoret uppfylls.

### <a name="deployifnotexists-evaluation"></a>DeployIfNotExists utvärdering

DeployIfNotExists körs även när en Resursleverantör hanterades en skapa eller uppdatera begäran till en resurs och returnerade en statuskod för lyckade. Effekten utlöses om det finns inga relaterade resurser eller om resurserna som definieras av **ExistenceCondition** inte utvärderas till SANT. När effekten utlöses har en för malldistribution körts.

Under en utvärderingscykel principdefinitioner med DeployIfNotExists effekt som matchar resurser är markerad som icke-kompatibla, men inga åtgärder utförs på den här resursen.

### <a name="deployifnotexists-properties"></a>DeployIfNotExists egenskaper

Den **information** egenskap DeployIfNotExists effekter har subegenskaper som definierar de relaterade resurserna till matchning och malldistribution ska köras.

- **Typen** [krävs]
  - Anger typ av relaterade resurs så att den matchar.
  - Startar genom att hämta en resurs under den **om** villkoret resurs och frågor i samma resursgrupp som det **om** condition-resurs.
- **Namnet** (valfritt)
  - Anger det exakta namnet av resursen ska matcha och gör att principen att hämta en specifik resurs i stället för alla resurser för den angivna typen.
- **ResourceGroupName** (valfritt)
  - Tillåter matchning av relaterade resursen ska hämtas från en annan resursgrupp.
  - Gäller inte om **typen** är en resurs som är under den **om** condition-resurs.
  - Standardvärdet är den **om** villkor resursens resursgruppen.
  - Om en för malldistribution har körts, har den distribuerats i resursgruppen för det här värdet.
- **ExistenceScope** (valfritt)
  - Tillåtna värden är _prenumeration_ och _ResourceGroup_.
  - Anger det scope där att hämta relaterade resursen ska matcha från.
  - Gäller inte om **typen** är en resurs som är under den **om** condition-resurs.
  - För _ResourceGroup_, skulle begränsa till den **om** villkoret resurs, resursgrupp eller resursgruppen som anges i **ResourceGroupName**.
  - För _prenumeration_, frågar hela prenumerationen för relaterad resurs.
  - Standardvärdet är _ResourceGroup_.
- **ExistenceCondition** (valfritt)
  - Om inget annat anges, alla relaterade resurs av **typen** uppfyller effekt och inte utlöser distributionen.
  - Använder samma språk som principregel för den **om** villkor, men utvärderas mot varje relaterad resurs individuellt.
  - Om alla matchande relaterade resurser evalueras till true, effekten är nöjd och utlöser inte distributionen.
  - Kan använda [field()] för att kontrollera överensstämmelse med värden i den **om** villkor.
  - Till exempel kan användas för att kontrollera att den överordnade resursen (i det **om** villkor) finns på samma resursplats som matchande relaterad resurs.
- **Distribution** [krävs]
  - Den här egenskapen ska innehålla den fullständiga malldistributionen som den skulle skickas till den `Microsoft.Resources/deployments` PLACERA API. Mer information finns i [distributioner REST API](/rest/api/resources/deployments).

  > [!NOTE]
  > Alla funktioner i den **distribution** egenskapen utvärderas som komponenter i mallen inte principen. Undantaget är den **parametrar** egenskap som skickar värden från principen för mallen. Den **värdet** i det här avsnittet under en mall för parameternamn som används för att utföra det här värdet som skickas (se _fullDbName_ DeployIfNotExists exempel).

### <a name="deployifnotexists-example"></a>DeployIfNotExists-exempel

Exempel: Utvärderar SQL Server-databaser för att avgöra om transparentDataEncryption är aktiverad. Om inte, sedan en distribution för att den körs.

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

En resurs kan påverkas av flera tilldelningar. Tilldelningarna kan vara i samma definitionsområde (specifik resurs, resursgrupp, prenumeration eller hanteringsgruppen) eller på olika omfång. Var och en av dessa tilldelningar är också troligt att det har en annan effekt som definierats. Oavsett, utvärderas oberoende villkor och effekt för varje princip (tilldelas direkt eller som en del av ett initiativ). Till exempel om principen 1 har ett villkor som begränsar resursplats för prenumerationen A kan endast skapas med westus om du neka effekten och principen 2 har vara ett villkor som begränsar resursplats för resursgruppen B (som är i prenumerationen A) till bara skapade i 'eastus' gälla för granskning är tilldelad, resulterande resultatet skulle vara::

- Alla resurser i resursgruppen B i 'eastus' redan är kompatibelt med princip 2, men markerad som icke-kompatibla till principen 1.
- Alla resurser i resursgruppen B inte i 'eastus' redan kommer att markeras som icke-kompatibla principen 2 och skulle också markeras inte kompatibelt med principen 1 om inget 'westus'.
- Alla nya resurser i prenumerationen A skulle inte i 'westus' avvisas av principen 1.
- Alla nya resurser i prenumerationen A / resursgrupp B i 'westus' skulle markeras som icke-kompatibla princip 2, men skulle skapas (kompatibelt med 1 och 2 är gransknings- och neka inte).

Om både 1 och 2 hade effekten av deny situation skulle ändra till:

- Alla resurser i resursgruppen B inte i 'eastus' redan markeras som icke-kompatibla principen 2.
- Alla resurser i resursgruppen B inte i 'westus' redan markeras som icke-kompatibla till principen 1.
- Alla nya resurser i prenumerationen A skulle inte i 'westus' avvisas av principen 1.
- Alla nya resurser i prenumerationen A / resursgruppen B skulle avvisas (eftersom dess plats inte kunde uppfyllas både 1 och 2).

Eftersom varje tilldelning individuellt utvärderas det inte finns en möjlighet för en resurs till slip via en lucka på grund av skillnader i omfånget. Därför resultatet av lager principer eller principer överlappar varandra anses vara **kumulativa mest restriktiva**. Med andra ord kan en resurs du vill skapa blockerats på grund av överlappande och motstridiga principer, till exempel exemplet ovan om både 1 och 2 hade en neka-effekt. Om du fortfarande behöver resurs ska skapas i målområdet granska undantag på varje tilldelning för att säkerställa rätt principer påverkar rätt scope.

## <a name="next-steps"></a>Nästa steg

Nu när du har en bättre förståelse av principen definition effekter granska princip-exempel:

- Granska fler exempel på [Azure Policy-exempel](json-samples.md).
