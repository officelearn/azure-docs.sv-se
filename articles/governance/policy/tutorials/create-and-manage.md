---
title: 'Självstudiekurs: Skapa principer för att upprätthålla efterlevnad'
description: I den här självstudien använder du principer för att tillämpa standarder, kontrollera kostnader, upprätthålla säkerhet och införa företagsomfattande designprinciper.
ms.date: 03/24/2020
ms.topic: tutorial
ms.openlocfilehash: 15a6c3df26938332d42ecbcfff43f958577062c4
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239934"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Självstudiekurs: Skapa och hantera principer för att upprätthålla efterlevnad

Det är viktigt att du förstår hur du skapar och hanterar principer i Azure för att du ska fortsätta att efterleva företagets standarder och servicenivåavtal. I den här självstudien lär du dig att använda Azure Policy för att utföra några av de vanligaste uppgifterna som att skapa, tilldela och hantera principer i organisationen, exempelvis:

> [!div class="checklist"]
> - Tilldela en princip för att genomdriva ett villkor för resurser som du skapar i framtiden
> - Skapa och tilldela en initiativdefinition för att spåra efterlevnad för flera resurser
> - Lösa en icke-kompatibel eller nekad resurs
> - Implementera en ny princip i en organisation

Om du vill tilldela en princip för att identifiera dina befintliga resursers efterlevnadstillstånd går artiklarna i snabbstarten igenom det.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="assign-a-policy"></a>Tilldela en princip

Det första steget för att tillämpa efterlevnad av Azure Policy är att tilldela en principdefinition. En principdefinition anger enligt vilket villkor en princip tillämpas och vilken åtgärd som ska vidtas. I det här exemplet tilldelar du den inbyggda principdefinitionen _Ärva en tagg från resursgruppen om_ det saknas för att lägga till den angivna taggen med dess värde från den överordnade resursgruppen till nya eller uppdaterade resurser som saknas taggen.

1. Gå till Azure-portalen för att tilldela principer. Sök efter och välj **Policy**.

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="Sök efter policy i sökfältet" border="false":::

1. Välj **Tilldelningar** till vänster på sidan Azure Policy. En tilldelning är en princip som tilldelats ett specifikt område.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="Välj tilldelningar från sidan Principöversikt" border="false":::

1. Välj **Tilldela princip** längst upp på sidan **Princip – Tilldelningar**.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="Tilldela en principdefinition från sidan Tilldelningar" border="false":::

1. På fliken **Tilldela princip** och **Grunderna** väljer du **Scopet** genom att välja ellipsen och välja antingen en hanteringsgrupp eller prenumeration. Du kan även välja en resursgrupp. En omfattning avgör vilka resurser eller grupper med resurser som principtilldelningen används på.
   Välj sedan **Välj** längst ned på **sidan Scope.**

   I det här exemplet används **Contoso-prenumerationen**. Din prenumeration skiljer sig.

1. Resurser som kan uteslutas baserat på **omfång**. **Undantag** startar på en nivå som är lägre än nivån för **Omfång**. **Undantag** är valfria, så lämna det tomt just nu.

1. Välj ellipsen **Principdefinition** för att öppna listan med tillgängliga definitioner. Du kan filtrera **principdefinitionstypen** till _Inbyggd_ om du vill visa alla och läsa deras beskrivningar.

1. Välj **Ärva en tagg i resursgruppen om den saknas**. Om du inte hittar den direkt skriver du **ärva en tagg** i sökrutan och trycker sedan på RETUR eller väljer ut från sökrutan.
   Välj **Välj** längst ned på sidan **Tillgängliga definitioner** när du har hittat och valt principdefinitionen.

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="Använda sökfilter för att hitta en princip":::

1. **Tilldelningsnamn** fylls i automatiskt med namnet på principen som du valde, men du kan ändra det om du vill. I det här exemplet lämnar _du Inherit a tag från resursgruppen om det saknas_. Du kan också lägga till en valfri **Beskrivning**. Beskrivningen innehåller information om den här principtilldelningen.

1. Lämna **tvingande princip** som _aktiverat_. När den här inställningen _är inaktiverad_kan du testa resultatet av principen utan att utlösa effekten. Mer information finns i [tvingande läge](../concepts/assignment-structure.md#enforcement-mode).

1. **Tilldelad av** fylls automatiskt i baserat på vem som är inloggad. Det här fältet är valfritt, så du kan ange anpassade värden.

1. Välj fliken **Parametrar** högst upp i guiden.

1. För **Taggnamn**anger du _Miljö_.

1. Välj fliken **Reparation** högst upp i guiden.

1. Lämna **Skapa en reparationsuppgift** avmarkerad. I den här rutan kan du skapa en aktivitet för att ändra befintliga resurser utöver nya eller uppdaterade resurser. Mer information finns i [åtgärda resurser](../how-to/remediate-resources.md).

1. **Skapa en hanterad identitet** kontrolleras automatiskt eftersom den här principdefinitionen använder [ändringseffekten.](../concepts/effects.md#modify) **Behörigheter** anges _till Deltagare_ automatiskt baserat på principdefinitionen. Mer information finns i avsnitten om [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md) och [hur reparationssäkerhet fungerar](../how-to/remediate-resources.md#how-remediation-security-works).

1. Välj fliken **Granska + skapa** högst upp i guiden.

1. Granska dina val och välj sedan **Skapa** längst ned på sidan.

## <a name="implement-a-new-custom-policy"></a>Implementera en ny anpassad princip

Nu när du har tilldelat en inbyggd principdefinition kan du göra mer med Azure Policy. Sedan skapar du en ny anpassad princip för att spara kostnader genom att kontrollera att de virtuella datorer som skapats i din miljö inte kan vara i G-serien. På så sätt nekas varje gång en begäran från en användare i organisationen som försöker skapa en virtuell dator i G-serien.

1. Välj **Definitioner** under **Redigering** till vänster på sidan Azure Policy.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Definitionssida under Redigeringsgrupp" border="false":::

1. Välj **+ Principdefinition** överst på sidan. Den här knappen öppnar sidan **Principdefinition**.

1. Ange följande information:

   - Hanteringsgruppen eller prenumerationen där principdefinitionen sparas. Välj **Definitionens plats** med ellipsen.

     > [!NOTE]
     > Om du tänker tillämpa denna principdefinition på flera prenumerationer, måste platsen vara en hanteringsgrupp som innehåller de prenumerationer som du tilldelar principen. Detsamma gäller för en initiativdefinition.

   - Namnet på principdefinitionen - _*_Kräv virtuella SKU:er som är mindre än G-serien_
   - Beskrivning av vad principdefinitionen är avsedd att göra – _Den här principdefinitionen tvingar alla virtuella datorer som är skapade i omfånget att ha SKU:er som är mindre än G-serien för att minska kostnaderna._
   - Välj mellan befintliga alternativ (som _Compute_), eller skapa en ny kategori för den här principdefinitionen.
   - Kopiera följande JSON-kod och uppdatera den sedan efter dina behov med:
      - Principparametrarna.
      - Principens regler/villkor, i det här fallet – VM SKU-storlek som motsvarar G-serien
      - Principens effekt, i det här fallet – **Neka**.

   Så här ska din JSON se ut. Klistra in den reviderade koden i Azure-portalen.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   Fältegenskapen i principregeln måste vara ett värde som stöds. _field_ En fullständig lista med värden finns i [fält för principdefinitionsstruktur](../concepts/definition-structure.md#fields). Ett exempel på ett alias kan vara `"Microsoft.Compute/VirtualMachines/Size"`.

   Fler Azure-principexempel finns i [Azure Policy-mallar](../samples/index.md).

1. Välj **Spara**.

## <a name="create-a-policy-definition-with-rest-api"></a>Skapa en principdefinition med REST API

Du kan skapa en princip med REST API för Azure-principdefinitioner. Med REST API kan du skapa och ta bort principdefinitioner och få information om befintliga definitioner. Om du vill skapa en principdefinition använder du följande exempel:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Inkludera en begärantext som liknar följande exempel:

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Skapa en principdefinition med PowerShell

Innan du fortsätter med PowerShell-exemplet kontrollerar du att du har installerat den senaste versionen av Azure PowerShell Az-modulen.

Du kan skapa en principdefinition med cmdleten `New-AzPolicyDefinition`.

Om du vill skapa en principdefinition från en fil skickar du sökvägen till filen. Använd följande exempel för en extern fil:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Använd följande exempel för en lokal fil:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Om du vill skapa en principdefinition med en infogad regel använder du följande exempel:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

Utdata lagras i ett `$definition`-objekt som används under principtilldelningen. I följande exempel skapas en principdefinition som innehåller parametrarna:

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Visa principdefinitioner med PowerShell

Om du vill visa alla principdefinitioner i din prenumeration använder du följande kommando:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Den returnerar alla tillgängliga principdefinitioner, inklusive inbyggda principer. Varje princip returneras i följande format:

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Skapa en principdefinition med Azure CLI

Du kan skapa en principdefinition `az policy definition` med Hjälp av Azure CLI med kommandot. Om du vill skapa en principdefinition med en infogad regel använder du följande exempel:

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Visa principdefinitioner med Azure CLI

Om du vill visa alla principdefinitioner i din prenumeration använder du följande kommando:

```azurecli-interactive
az policy definition list
```

Den returnerar alla tillgängliga principdefinitioner, inklusive inbyggda principer. Varje princip returneras i följande format:

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Skapa och tilldela en initiativdefinition

Med en initiativdefinition kan du gruppera flera principdefinitioner för att uppnå ett övergripande mål. Ett initiativ utvärderar resurser inom omfånget för tilldelningen för efterlevnad med tillhörande principer. Se [Översikt över Azure Policy](../overview.md) för mer information om initiativdefinitioner.

### <a name="create-an-initiative-definition"></a>Skapa en initiativdefinition

1. Välj **Definitioner** under **Redigering** till vänster på sidan Azure Policy.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Välj definition på sidan Definitioner" border="false":::

1. Välj **+ Initiativdefinition** överst på sidan för att öppna sidan **Initiativdefinition**.

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="Sidan Granska definition av initiativ" border="false":::

1. Använd ellipsen **Definitionens plats ** och välj en hanteringsgrupp eller en prenumeration där definitionen ska lagras. Om föregående sida begränsades till en enskild hanteringsgrupp eller prenumeration så fylls **definitionsplats** i automatiskt. När du har valt **fylls tillgängliga definitioner** i.

1. Ange **Namn** och **Beskrivning** för initiativet.

   Det här exemplet kontrollerar att resurser efterlever principdefinitioner om säkerhet. Namnge initiativet **Bli säker** och ange beskrivningen som: **Det här initiativet har skapats för att hantera principdefinitioner associerade med att säkra resurser**.

1. Välj bland befintliga alternativ eller skapa en ny kategori i **Kategori**.

1. Bläddra igenom listan **Tillgängliga definitioner** (högre halvan av sidan **Initiativdefinition**) och välj de principdefinitioner som du vill lägga till i initiativet. För initiativet **Hämta säkerhet** lägger du till följande inbyggda **+** principdefinitioner genom att välja bredvid principdefinitionsinformationen eller välja en principdefinitionsrad och sedan alternativet **+ Lägg till** på informationssidan:

   - Tillåtna platser
   - Övervaka saknade slutpunktsskydd i Azure Security Center
   - Regler för nätverkssäkerhetsgrupp för internetvända virtuella datorer bör skärpas
   - Azure Backup bör aktiveras för virtuella datorer
   - Diskkryptering bör tillämpas på virtuella datorer

   När du har valt principdefinitionen i listan läggs var och en till under **Kategori**.

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="Granska parametrar för initiativdefinition" border="false":::

1. Om en principdefinition som läggs till i initiativet har parametrar visas de under principnamnet i området **Under Kategoriområde.** _Värdet_ kan anges till antingen Ange värde (hårdkodat för alla tilldelningar i initiativet) eller Använd initiativparametern (ställs in under varje initiativtilldelning). Om "Ange värde" är markerat kan listrutan till höger om _Värden_ ange eller välja värden. Om du väljer Använd initiativparametern så visas ett nytt avsnitt **Initiativparameter** där du kan definiera den parameter som ställs in under initiativtilldelningen. Tillåtna värden för den här initiativparametern kan ytterligare begränsa vad du kan ange under initiativtilldelningen.

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="Ändra parametrar för initiativdefinition från tillåtna värden" border="false":::

   > [!NOTE]
   > När det gäller vissa parametrar av typen `strongType` går det inte att automatiskt fastställa listan med värden. I de här fallen visas en ellips till höger om parameterraden. Om du väljer den öppnas&lt;sidan&gt;Parameteromfattning ( parameternamn ). På den här sidan väljer du den prenumeration som ska användas för att tillhandahålla värdealternativen. Det här parameterområdet används bara när initiativdefinitionen skapas. Den påverkar inte principutvärderingen eller initiativets omfattning efter tilldelningen.

   Ange parametern Tillåten plats till "Östra US 2" och lämna de andra som standard "AuditifNotExists".

1. Välj **Spara**.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Skapa en principinitiativdefinition med Azure CLI

Du kan skapa en principinitiativdefinition `az policy set-definition` med Hjälp av Azure CLI med kommandot. Om du vill skapa en definition av principinitiativ med en befintlig principdefinition använder du följande exempel:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Skapa en principinitiativdefinition med Azure PowerShell

Du kan skapa en principinitiativdefinition med `New-AzPolicySetDefinition` Azure PowerShell med cmdlet. Om du vill skapa en definition av politiska initiativ med `VMPolicySet.json`en befintlig principdefinition använder du följande definitionsfil för principinitiativ som:

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>Tilldela en initiativdefinition

1. Välj **Definitioner** under **Redigering** till vänster på sidan Azure Policy.

1. Leta upp initiativdefinitionen **Get Secure** som du skapade tidigare och markera den. Välj **Tilldela** överst på sidan för att öppna sidan **Bli säker: tilldela initiativ**.

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="Tilldela en definition från definitionssidan för initiativ" border="false":::

   Du kan också högerklicka på den valda raden eller välja ellipsen i slutet av raden för en sammanhangsberoende meny. Välj sedan **Tilldela**.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="Alternativa alternativ för ett initiativ" border="false":::

1. Fyll i sidan **Get Secure: Assign Initiative** genom att ange följande exempelinformation. Du kan använda din egen information.

   - Omfång: hanteringsgruppen eller prenumerationen som du sparade initiativet till blir standard.
     Du kan ändra omfånget för att tilldela initiativet till en prenumeration eller resursgrupp inom lagringsplatsen.
   - Undantag: Konfigurera några resurser inom omfånget för att förhindra att initiativtilldelningen tillämpas på dem.
   - Initiativdefinition och tilldelningsnamn: Get Secure (förifylld med namnet på initiativet som tilldelats).
   - Beskrivning: Initiativtilldelningen är skräddarsydd för att tillämpa den här gruppen med principdefinitioner.
   - Tvingande princip: Lämna som standard _Aktiverad_.
   - Tilldelad av: anges automatiskt baserat på vem som är inloggad. Det här fältet är valfritt, så du kan ange anpassade värden.

1. Välj fliken **Parametrar** högst upp i guiden. Om du har konfigurerat en initiativparameter i tidigare steg anger du ett värde här.

1. Välj fliken **Reparation** högst upp i guiden. Lämna **Skapa en hanterad identitet** avmarkerat. Den här rutan _måste_ markeras när principen eller initiativet som tilldelas innehåller en princip med [deployIfNotExists](../concepts/effects.md#deployifnotexists) eller [ändra](../concepts/effects.md#modify) effekter. Eftersom den princip som används för den här självstudien inte gör det kan du lämna den tom. Mer information finns i avsnitten om [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md) och [hur reparationssäkerhet fungerar](../how-to/remediate-resources.md#how-remediation-security-works).

1. Välj fliken **Granska + skapa** högst upp i guiden.

1. Granska dina val och välj sedan **Skapa** längst ned på sidan.

## <a name="check-initial-compliance"></a>Kontrollera inledande efterlevnad

1. Välj **Efterlevnad** till vänster på Azure Policy-sidan.

1. Leta reda på initiativet **Hämta säkert.** Det är sannolikt fortfarande i _Efterlevnadstillståndet_**Inte startat**.
   Välj initiativet för att få fullständig information om hur tilldelningen fortskrider.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="Sidan för efterlevnad av initiativet – utvärderingar har inte startats" border="false":::

1. När initiativtilldelningen har slutförts, uppdateras sidan för efterlevnad med den _Efterlevnadstillståndet_**Efterlever**.

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="Sida för efterlevnad av initiativ- resurser" border="false":::

1. Om du väljer en princip på sidan för efterlevnad av initiativ öppnas sidan för efterlevnadsinformation för den principen. Den här sidan ger information på resursnivån för efterlevnad.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Undanta en icke-kompatibel eller nekad resurs med Exkludering

När du har tilldelat ett principinitiativ för att kräva en viss plats nekas alla resurser som skapas på en annan plats. I det här avsnittet går du igenom att lösa en nekad begäran om att skapa en resurs genom att skapa ett undantag för en enskild resursgrupp. Undantaget förhindrar att principen (eller initiativet) efterlevs i den resursgruppen. I följande exempel tillåts alla platser i den uteslutna resursgruppen. Ett undantag kan gälla för en prenumeration, en resursgrupp eller en enskild resurs.

Distributioner som förhindras av en tilldelad princip eller ett till tilldelat initiativ kan visas i resursgruppen som distributionen riktar sig till: Välj **distributioner** till vänster på sidan och välj sedan **distributionsnamnet för** den misslyckade distributionen. Resursen som nekades visas med statusen _Förbjuden_. Om du vill bestämma vilken princip eller vilket initiativ och vilken tilldelning som nekade resursen väljer du **Misslyckades. Klicka här för mer information ->** på sidan Distributionsöversikt. Ett fönster öppnas till höger om sidan med felinformationen. Under **Felinformation** finns GUID för de relaterade principobjekten.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="Distributionen nekas av principtilldelningen" border="false":::

På sidan Azure Policy: Välj **efterlevnad** till vänster på sidan och välj initiativet **Hämta säker** princip. På den här sidan ökar antalet **neka** blockerade resurser. Under fliken **Händelser** finns information om vem som försökte skapa eller distribuera resursen som nekades av principdefinitionen.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="Efterlevnadsöversikt för en tilldelad princip" border="false":::

I det här exemplet utförde Trent Baker, en av Contosos seniora virtualiseringsspecialister, obligatoriskt arbete. Vi måste ge Trent ett utrymme för ett undantag. Skapade en ny resursgrupp, **LocationsExcluded**och ger den sedan ett undantag till den här principtilldelningen.

### <a name="update-assignment-with-exclusion"></a>Uppdatera tilldelning med undantag

1. Välj **Tilldelningar** under **Redigering** till vänster om Azure Policy-sidan.

1. Bläddra igenom alla principtilldelningar och öppna tilldelningen _Hämta säker_ princip.

1. Ange **uteslutning** genom att välja ellipsen och välja resursgruppen som ska exkluderas, _PlatserExcluded_ i det här exemplet. Välj **Lägg till i markerat scope** och välj sedan **Spara**.

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="Lägga till en utesluten resursgrupp i principtilldelningen" border="false":::

   > [!NOTE]
   > Beroende på principdefinitionen och dess effekt kan uteslutningen också beviljas till specifika resurser inom en resursgrupp inom tilldelningens omfattning. Eftersom en **neka-effekt** användes i den här självstudien skulle det inte vara meningsfullt att ange uteslutning på en viss resurs som redan finns.

1. Välj **Granska + spara** och välj sedan **Spara**.

I det här avsnittet löste du den nekade begäran genom att skapa ett undantag på en enskild resursgrupp.

## <a name="clean-up-resources"></a>Rensa resurser

Om du har arbetat med resurser från den här självstudien gör du så här för att ta bort någon av de principtilldelningar eller definitioner som skapats ovan:

1. Välj **Definitioner** (eller **Tilldelningar** om du ska ta bort en tilldelning) under **Redigering** till vänster på sidan Azure Policy.

1. Sök efter den nya initiativ- eller principdefinition (eller tilldelning) som du vill ta bort.

1. Högerklicka på raden eller välj ellipserna i slutet av definitionen (eller tilldelningen) och välj **Ta bort definition** (eller **Ta bort tilldelning**).

## <a name="review"></a>Granska

I den här självstudien har du genomfört följande uppgifter:

> [!div class="checklist"]
> - Tilldelat en princip för att genomdriva ett villkor för resurser som du skapar i framtiden
> - Skapat och tilldelat en initiativdefinition för att spåra efterlevnad för flera resurser
> - Löst en icke-kompatibel eller nekad resurs
> - Implementerat en ny princip i en organisation

## <a name="next-steps"></a>Nästa steg

Mer information om principdefinitionernas strukturer finns i den här artikeln:

> [!div class="nextstepaction"]
> [Azure Policy-definitionsstruktur](../concepts/definition-structure.md)