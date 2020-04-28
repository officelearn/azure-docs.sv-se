---
title: 'Självstudie: skapa principer för att genomdriva efterlevnad'
description: I den här självstudien använder du principer för att genomdriva standarder, kontrol lera kostnader, upprätthålla säkerhet och införa företagets breda design principer.
ms.date: 03/24/2020
ms.topic: tutorial
ms.openlocfilehash: dcebbbfcc2f86ace7ea4400a2fdb6f1392f4efe6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82190834"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Självstudie: skapa och hantera principer för att genomdriva efterlevnad

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

Det första steget för att tillämpa efterlevnad av Azure Policy är att tilldela en principdefinition. En principdefinition anger enligt vilket villkor en princip tillämpas och vilken åtgärd som ska vidtas. I det här exemplet tilldelar du den inbyggda princip definitionen som kallas _Ärv en tagg från resurs gruppen om den saknas_ för att lägga till den angivna taggen med värdet från den överordnade resurs gruppen till nya eller uppdaterade resurser som saknar taggen.

1. Gå till Azure Portal för att tilldela principer. Sök efter och välj **princip**.

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="Sök efter princip i Sök fältet" border="false":::

1. Välj **Tilldelningar** till vänster på sidan Azure Policy. En tilldelning är en princip som tilldelats ett specifikt område.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="Sidan Välj tilldelningar från princip översikt" border="false":::

1. Välj **Tilldela princip** längst upp på sidan **Princip – Tilldelningar**.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="Tilldela en princip definition från sidan tilldelningar" border="false":::

1. På fliken **tilldela princip** sidan och **grunderna** väljer du **omfattningen** genom att välja ellipsen och välja antingen en hanterings grupp eller en prenumeration. Du kan även välja en resursgrupp. En omfattning avgör vilka resurser eller grupper med resurser som principtilldelningen används på.
   Välj sedan **Välj** längst ned på sidan **omfång** .

   I det här exemplet används **Contoso-prenumerationen**. Din prenumeration skiljer sig.

1. Resurser som kan uteslutas baserat på **omfång**. **Undantag** startar på en nivå som är lägre än nivån för **Omfång**. **Undantag** är valfria, så lämna det tomt just nu.

1. Välj ellipsen **Principdefinition** för att öppna listan med tillgängliga definitioner. Du kan filtrera princip definitions **typen** till _inbyggd_ för att visa alla och läsa deras beskrivningar.

1. Välj **Ärv en tagg från resurs gruppen om det saknas**. Om du inte kan hitta den direkt skriver du **Ärv en tagg** till sökrutan och trycker sedan på RETUR eller väljer ut från sökrutan.
   Välj **Välj** längst ned på sidan **tillgängliga definitioner** när du har hittat och valt princip definitionen.

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="Använd Sök filter för att hitta en princip":::

1. **Tilldelningsnamn** fylls i automatiskt med namnet på principen som du valde, men du kan ändra det om du vill. I det här exemplet ska du lämna _Ärv en tagg från resurs gruppen om den saknas_. Du kan också lägga till en valfri **Beskrivning**. Beskrivningen innehåller information om den här principtilldelningen.

1. Lämna **princip tvång** som _aktive rad_. När den här inställningen är _inaktive rad_kan du testa resultatet av principen utan att utlösa resultatet. Mer information finns i [tvingande läge](../concepts/assignment-structure.md#enforcement-mode).

1. **Tilldelad av** fylls automatiskt i baserat på vem som är inloggad. Det här fältet är valfritt, så du kan ange anpassade värden.

1. Välj fliken **parametrar** överst i guiden.

1. Ange _miljö_för **taggnamn**.

1. Välj fliken **reparation** överst i guiden.

1. Lämna **skapa en reparations uppgift** avmarkerad. Med den här rutan kan du skapa en uppgift för att ändra befintliga resurser utöver nya eller uppdaterade resurser. Mer information finns i [åtgärda resurser](../how-to/remediate-resources.md).

1. **Skapa en hanterad identitet** kontrol leras automatiskt eftersom den här princip definitionen använder [ändrings](../concepts/effects.md#modify) resultatet. **Behörigheter** anges till _Contributor_ automatiskt baserat på princip definitionen. Mer information finns i avsnitten om [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md) och [hur reparationssäkerhet fungerar](../how-to/remediate-resources.md#how-remediation-security-works).

1. Välj fliken **Granska + skapa** överst i guiden.

1. Granska dina val och välj sedan **skapa** längst ned på sidan.

## <a name="implement-a-new-custom-policy"></a>Implementera en ny anpassad princip

Nu när du har tilldelat en inbyggd principdefinition kan du göra mer med Azure Policy. Sedan skapar du en ny anpassad princip för att spara kostnader genom att kontrollera att de virtuella datorer som skapats i din miljö inte kan vara i G-serien. På så sätt nekas varje gång en begäran från en användare i organisationen som försöker skapa en virtuell dator i G-serien.

1. Välj **Definitioner** under **Redigering** till vänster på sidan Azure Policy.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Definitions sidan under redigerings gruppen" border="false":::

1. Välj **+ Principdefinition** överst på sidan. Den här knappen öppnar sidan **Principdefinition**.

1. Ange följande information:

   - Hanteringsgruppen eller prenumerationen där principdefinitionen sparas. Välj **Definitionens plats** med ellipsen.

     > [!NOTE]
     > Om du tänker tillämpa denna principdefinition på flera prenumerationer, måste platsen vara en hanteringsgrupp som innehåller de prenumerationer som du tilldelar principen. Detsamma gäller för en initiativdefinition.

   - Namnet på princip definitionen-_ *_KRÄV VM SKU: er som är mindre än G-serien_
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

   _Fält_ egenskapen i princip regeln måste vara ett värde som stöds. En fullständig lista med värden finns i [struktur fält för princip definition](../concepts/definition-structure.md#fields). Ett exempel på ett alias kan vara `"Microsoft.Compute/VirtualMachines/Size"`.

   Om du vill visa fler Azure Policy exempel, se [Azure policy exempel](../samples/index.md).

1. Välj **Spara**.

## <a name="create-a-policy-definition-with-rest-api"></a>Skapa en principdefinition med REST API

Du kan skapa en princip med REST API för Azure Policy definitioner. Med REST API kan du skapa och ta bort principdefinitioner och få information om befintliga definitioner. Om du vill skapa en principdefinition använder du följande exempel:

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

Innan du fortsätter med PowerShell-exemplet måste du kontrol lera att du har installerat den senaste versionen av modulen Azure PowerShell AZ.

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

Du kan skapa en princip definition med hjälp av Azure CLI `az policy definition` med kommandot. Om du vill skapa en principdefinition med en infogad regel använder du följande exempel:

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

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Välj definition från sidan definitioner" border="false":::

1. Välj **+ Initiativdefinition** överst på sidan för att öppna sidan **Initiativdefinition**.

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="Sidan granska initiativ definition" border="false":::

1. Använd ellipsen **Definitionens plats ** och välj en hanteringsgrupp eller en prenumeration där definitionen ska lagras. Om föregående sida begränsades till en enskild hanteringsgrupp eller prenumeration så fylls **definitionsplats** i automatiskt. När det är markerat fylls **tillgängliga definitioner** i.

1. Ange **Namn** och **Beskrivning** för initiativet.

   Det här exemplet kontrollerar att resurser efterlever principdefinitioner om säkerhet. Namnge initiativet **Bli säker** och ange beskrivningen som: **Det här initiativet har skapats för att hantera principdefinitioner associerade med att säkra resurser**.

1. Välj bland befintliga alternativ eller skapa en ny kategori i **Kategori**.

1. Bläddra igenom listan **Tillgängliga definitioner** (högre halvan av sidan **Initiativdefinition**) och välj de principdefinitioner som du vill lägga till i initiativet. För att **få säker** initiativ lägger du till följande inbyggda princip definitioner genom att välja **+** bredvid princip definitions informationen eller välja en princip definitions rad och sedan alternativet **+ Lägg till** på sidan information:

   - Tillåtna platser
   - Övervaka saknade Endpoint Protection i Azure Security Center
   - Regler för nätverks säkerhets grupper för virtuella datorer som riktas mot Internet bör vara skärpta
   - Azure Backup ska vara aktiverat för Virtual Machines
   - Disk kryptering bör tillämpas på virtuella datorer

   När du har valt princip definitionen i listan läggs varje **kategori**till nedan.

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="Granska initiativ definitions parametrar" border="false":::

1. Om en princip definition som läggs till i initiativet har parametrar visas de under princip namnet i avsnittet **kategori** . _Värdet_ kan anges till antingen Ange värde (hårdkodat för alla tilldelningar i initiativet) eller Använd initiativparametern (ställs in under varje initiativtilldelning). Om Ange värde är markerat kan List rutan till höger om _värde (n)_ ange eller välja värden. Om du väljer Använd initiativparametern så visas ett nytt avsnitt **Initiativparameter** där du kan definiera den parameter som ställs in under initiativtilldelningen. Tillåtna värden för den här initiativparametern kan ytterligare begränsa vad du kan ange under initiativtilldelningen.

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="Ändra initiativ definitions parametrar från tillåtna värden" border="false":::

   > [!NOTE]
   > När det gäller vissa parametrar av typen `strongType` går det inte att automatiskt fastställa listan med värden. I de här fallen visas en ellips till höger om parameterraden. Om du markerar den öppnas sidan parameter omfattning&lt;(parameter&gt;namn). På den här sidan väljer du den prenumeration som ska användas för att tillhandahålla värdealternativen. Det här parameterområdet används bara när initiativdefinitionen skapas. Den påverkar inte principutvärderingen eller initiativets omfattning efter tilldelningen.

   Ange parametern "tillåtna platser" till "USA, östra 2" och lämna den andra som standard "AuditifNotExists".

1. Välj **Spara**.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Skapa en regel initiativs definition med Azure CLI

Du kan skapa en princip initiativ definition med hjälp av Azure CLI `az policy set-definition` med kommandot. Använd följande exempel för att skapa en princip initiativ definition med en befintlig princip definition:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Skapa en regel initiativ definition med Azure PowerShell

Du kan skapa en princip initiativ definition med hjälp av Azure PowerShell `New-AzPolicySetDefinition` med cmdleten. Om du vill skapa en princip initiativ definition med en befintlig princip definition använder du följande definitions fil för `VMPolicySet.json`princip initiativ som:

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

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="Tilldela en definition från sidan initiativ definition" border="false":::

   Du kan också högerklicka på den markerade raden eller välja ellipsen i slutet av raden för en snabb meny. Välj sedan **Tilldela**.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="Alternativa alternativ för ett initiativ" border="false":::

1. Fyll i sidan **Get Secure: Assign Initiative** genom att ange följande exempelinformation. Du kan använda din egen information.

   - Omfång: hanteringsgruppen eller prenumerationen som du sparade initiativet till blir standard.
     Du kan ändra omfånget för att tilldela initiativet till en prenumeration eller resursgrupp inom lagringsplatsen.
   - Undantag: Konfigurera några resurser inom omfånget för att förhindra att initiativtilldelningen tillämpas på dem.
   - Initiativdefinition och tilldelningsnamn: Get Secure (förifylld med namnet på initiativet som tilldelats).
   - Beskrivning: Initiativtilldelningen är skräddarsydd för att tillämpa den här gruppen med principdefinitioner.
   - Princip tillämpning: låt standardvärdet vara _aktiverat_.
   - Tilldelad av: anges automatiskt baserat på vem som är inloggad. Det här fältet är valfritt, så du kan ange anpassade värden.

1. Välj fliken **parametrar** överst i guiden. Ange ett värde om du konfigurerade en initiativ parameter i föregående steg.

1. Välj fliken **reparation** överst i guiden. Lämna **Skapa en hanterad identitet** avmarkerat. Den här rutan _måste_ kontrol leras när principen eller initiativet som tilldelas innehåller en princip med [deployIfNotExists](../concepts/effects.md#deployifnotexists) eller [ändra](../concepts/effects.md#modify) effekter. Eftersom den princip som används för den här självstudien inte gör det kan du lämna den tom. Mer information finns i avsnitten om [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md) och [hur reparationssäkerhet fungerar](../how-to/remediate-resources.md#how-remediation-security-works).

1. Välj fliken **Granska + skapa** överst i guiden.

1. Granska dina val och välj sedan **skapa** längst ned på sidan.

## <a name="check-initial-compliance"></a>Kontrollera inledande efterlevnad

1. Välj **Efterlevnad** till vänster på Azure Policy-sidan.

1. Leta upp **säkerhets initiativet get** . Det är sannolikt fortfarande i _Efterlevnadstillståndet_**Inte startat**.
   Välj initiativet för att få fullständig information om tilldelningens förlopp.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="Sidan krav på initiativ-utvärderingar har inte startats" border="false":::

1. När initiativtilldelningen har slutförts, uppdateras sidan för efterlevnad med den _Efterlevnadstillståndet_**Efterlever**.

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="Sidan krav på initiativ – resurser är kompatibla" border="false":::

1. Om du väljer en princip på sidan efterlevnad öppnas sidan kompatibilitetsinformation för principen. Den här sidan ger information på resursnivån för efterlevnad.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Undanta en icke-kompatibel eller nekad resurs med Exkludering

När du har tilldelat ett princip initiativ för att kräva en särskild plats nekas alla resurser som har skapats på en annan plats. I det här avsnittet ska du gå igenom hur du löser en nekad begäran om att skapa en resurs genom att skapa ett undantag i en enda resurs grupp. Undantaget förhindrar tvång av principen (eller initiativet) för den resurs gruppen. I följande exempel tillåts alla platser i den undantagna resurs gruppen. Ett undantag kan gälla för en prenumeration, en resurs grupp eller enskilda resurser.

Distributioner som förhindras av en tilldelad princip eller ett initiativ kan visas i resurs gruppen som är målet för distributionen: Välj **distributioner** till vänster på sidan och välj **distributions namnet** för den misslyckade distributionen. Resursen som nekades visas med statusen _Förbjuden_. Om du vill fastställa principen eller initiativet och tilldelningen som nekade resursen väljer du **misslyckades. Klicka här om du vill ha mer information – >** på sidan distributions översikt. Ett fönster öppnas till höger om sidan med felinformationen. Under **Felinformation** finns GUID för de relaterade principobjekten.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="Distributionen nekas av principtilldelningen" border="false":::

På sidan Azure Policy: Välj **efterlevnad** på vänster sida av sidan och välj initiativet **Hämta säker** princip. På den här sidan finns det en ökning av antalet **nekade** resurser för blockerade resurser. Under fliken **händelser** finns information om vem som försökte skapa eller distribuera resursen som nekades av princip definitionen.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="Efterlevnadsöversikt för en tilldelad princip" border="false":::

I det här exemplet utförde Trent Baker, en av Contosos seniora virtualiseringsspecialister, obligatoriskt arbete. Vi måste bevilja Trent ett utrymme för ett undantag. Skapade en ny resurs grupp, **LocationsExcluded**och sedan ett undantag till den här princip tilldelningen.

### <a name="update-assignment-with-exclusion"></a>Uppdatera tilldelning med undantag

1. Välj **Tilldelningar** under **Redigering** till vänster om Azure Policy-sidan.

1. Bläddra igenom alla princip tilldelningar och öppna aktiviteten _Hämta säker_ princip.

1. Ange **undantag** genom att välja ellipsen och välja den resurs grupp som ska undantas, _LocationsExcluded_ i det här exemplet. Välj **Lägg till i det valda omfånget** och välj sedan **Spara**.

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="Lägg till en undantagen resurs grupp i princip tilldelningen" border="false":::

   > [!NOTE]
   > Beroende på princip definitionen och dess påverkan kan undantaget också beviljas till vissa resurser inom en resurs grupp inom tilldelningens omfattning. Som en **neka** -inverkan användes i den här självstudien, men det var inte klokt att ange undantaget för en speciell resurs som redan finns.

1. Välj **Granska och spara** och välj sedan **Spara**.

I det här avsnittet löste du den nekade begäran genom att skapa ett undantag på en enskild resursgrupp.

## <a name="clean-up-resources"></a>Rensa resurser

Om du har arbetat med resurser från den här självstudien kan du använda följande steg för att ta bort alla princip tilldelningar eller definitioner som skapats ovan:

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