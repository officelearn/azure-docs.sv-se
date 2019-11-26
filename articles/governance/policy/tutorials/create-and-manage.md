---
title: 'Tutorial: Build policies to enforce compliance'
description: In this tutorial, you use policies to enforce standards, control costs, maintain security, and impose enterprise wide design principles.
ms.date: 11/25/2019
ms.topic: tutorial
ms.openlocfilehash: 75a1d892a88f2b5bbdbec2a1b8d525245bb1e86f
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482365"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Tutorial: Create and manage policies to enforce compliance

Det är viktigt att du förstår hur du skapar och hanterar principer i Azure för att du ska fortsätta att efterleva företagets standarder och servicenivåavtal. I den här självstudien lär du dig att använda Azure Policy för att utföra några av de vanligaste uppgifterna som att skapa, tilldela och hantera principer i organisationen, exempelvis:

> [!div class="checklist"]
> - Tilldela en princip för att genomdriva ett villkor för resurser som du skapar i framtiden
> - Skapa och tilldela en initiativdefinition för att spåra efterlevnad för flera resurser
> - Lösa en icke-kompatibel eller nekad resurs
> - Implementera en ny princip i en organisation

Om du vill tilldela en princip för att identifiera dina befintliga resursers efterlevnadstillstånd går artiklarna i snabbstarten igenom det.

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="assign-a-policy"></a>Tilldela en princip

Det första steget för att tillämpa efterlevnad av Azure Policy är att tilldela en principdefinition. En principdefinition anger enligt vilket villkor en princip tillämpas och vilken åtgärd som ska vidtas. I det här exemplet ska du tilldela en inbyggd principdefinition som heter *Kräv SQL Server version 12.0*, för att tillämpa villkoret att alla SQL Server-databaser måste vara v12.0 för att vara kompatibla.

1. Go to the Azure portal to assign policies. Search for and select **Policy**.

   ![Search for Policy in the search bar](../media/create-and-manage/search-policy.png)

1. Välj **Tilldelningar** till vänster på sidan Azure Policy. En tilldelning är en princip som tilldelats ett specifikt område.

   ![Select Assignments from Policy Overview page](../media/create-and-manage/select-assignments.png)

1. Välj **Tilldela princip** längst upp på sidan **Princip – Tilldelningar**.

   ![Assign a policy definition from Assignments page](../media/create-and-manage/select-assign-policy.png)

1. On the **Assign Policy** page, select the **Scope** by selecting the ellipsis and selecting either a management group or subscription. Du kan även välja en resursgrupp. En omfattning avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Then select **Select** at the bottom of the **Scope** page.

   I det här exemplet används **Contoso-prenumerationen**. Din prenumeration skiljer sig.

1. Resurser som kan uteslutas baserat på **omfång**. **Undantag** startar på en nivå som är lägre än nivån för **Omfång**. **Undantag** är valfria, så lämna det tomt just nu.

1. Välj ellipsen **Principdefinition** för att öppna listan med tillgängliga definitioner. Du kan filtrera principdefinitionen **Typ** som *Inbyggt* om du vill se alla och läsa deras beskrivningar.

1. Välj **Kräv SQL Server version 12.0**. If you can't find it right away, type **require sql server** into the search box and then press ENTER or select out of the search box. Select **Select** at the bottom of the **Available Definitions** page once you have found and selected the policy definition.

   ![Use search filter to locate a policy](../media/create-and-manage/select-available-definition.png)

1. **Tilldelningsnamn** fylls i automatiskt med namnet på principen som du valde, men du kan ändra det om du vill. I det här exemplet låter du *Kräv SQL Server version 12.0* vara kvar. Du kan också lägga till en valfri **Beskrivning**. Beskrivningen innehåller information om den här principtilldelningen.
   **Tilldelad av** fylls automatiskt i baserat på vem som är inloggad. Det här fältet är valfritt, så du kan ange anpassade värden.

1. Lämna **Skapa en hanterad identitet** avmarkerat. Den här rutan _måste_ markeras när den princip eller det initiativ som tilldelas omfattar en princip med effekten [deployIfNotExists](../concepts/effects.md#deployifnotexists). Eftersom den princip som används för den här självstudien inte gör det kan du lämna den tom. Mer information finns i avsnitten om [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md) och [hur reparationssäkerhet fungerar](../how-to/remediate-resources.md#how-remediation-security-works).

1. Välj **Tilldela**.

## <a name="implement-a-new-custom-policy"></a>Implementera en ny anpassad princip

Nu när du har tilldelat en inbyggd principdefinition kan du göra mer med Azure Policy. Sedan skapar du en ny anpassad princip för att spara kostnader genom att kontrollera att de virtuella datorer som skapats i din miljö inte kan vara i G-serien. På så sätt nekas varje gång en begäran från en användare i organisationen som försöker skapa en virtuell dator i G-serien.

1. Välj **Definitioner** under **Redigering** till vänster på sidan Azure Policy.

   ![Definition page under Authoring group](../media/create-and-manage/definition-under-authoring.png)

1. Välj **+ Principdefinition** överst på sidan. Den här knappen öppnar sidan **Principdefinition**.

1. Ange följande information:

   - Hanteringsgruppen eller prenumerationen där principdefinitionen sparas. Välj **Definitionens plats** med ellipsen.

     > [!NOTE]
     > Om du tänker tillämpa denna principdefinition på flera prenumerationer, måste platsen vara en hanteringsgrupp som innehåller de prenumerationer som du tilldelar principen. Detsamma gäller för en initiativdefinition.

   - Namnet på principdefinitionen – *Kräv VM SKU:er som är mindre än G-serien*
   - Beskrivning av vad principdefinitionen är avsedd att göra – *Den här principdefinitionen tvingar alla virtuella datorer som är skapade i omfånget att ha SKU:er som är mindre än G-serien för att minska kostnaderna.*
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

   The *field* property in the policy rule must be one of the following values: Name, Type, Location, Tags, or an alias. Ett exempel på ett alias kan vara `"Microsoft.Compute/VirtualMachines/Size"`.

   Fler Azure-principexempel finns i [Azure Policy-mallar](../samples/index.md).

1. Välj **Spara**.

## <a name="create-a-policy-definition-with-rest-api"></a>Skapa en principdefinition med REST API

You can create a policy with the REST API for Azure Policy Definitions. Med REST API kan du skapa och ta bort principdefinitioner och få information om befintliga definitioner. Om du vill skapa en principdefinition använder du följande exempel:

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

Before proceeding with the PowerShell example, make sure you've installed the latest version of the Azure PowerShell Az module.

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

Du kan skapa en principdefinition med Azure CLI med kommandot för principdefinition. Om du vill skapa en principdefinition med en infogad regel använder du följande exempel:

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

   ![Select definition from the Definitions page](../media/create-and-manage/definition-under-authoring.png)

1. Välj **+ Initiativdefinition** överst på sidan för att öppna sidan **Initiativdefinition**.

   ![Review initiative definition page](../media/create-and-manage/initiative-definition.png)

1. Använd ellipsen **Definitionens plats**  och välj en hanteringsgrupp eller en prenumeration där definitionen ska lagras. Om föregående sida begränsades till en enskild hanteringsgrupp eller prenumeration så fylls **definitionsplats** i automatiskt.

1. Ange **Namn** och **Beskrivning** för initiativet.

   Det här exemplet kontrollerar att resurser efterlever principdefinitioner om säkerhet. Namnge initiativet **Bli säker** och ange beskrivningen som: **Det här initiativet har skapats för att hantera principdefinitioner associerade med att säkra resurser**.

1. Välj bland befintliga alternativ eller skapa en ny kategori i **Kategori**.

1. Bläddra igenom listan **Tillgängliga definitioner** (högre halvan av sidan **Initiativdefinition**) och välj de principdefinitioner som du vill lägga till i initiativet. For the **Get secure** initiative, add the following built-in policy definitions by selecting the **+** next to the policy definition information or selecting a policy definition row and then the **+ Add** option in the details page:

   - Kräv SQL Server version 12.0
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   När du har valt principdefinitionen i listan läggs den till under **Principer och parametrar**.

   ![Review initiative definition parameters](../media/create-and-manage/initiative-definition-2.png)

1. Om en principdefinition som läggs till i initiativet har parametrar visas de under namnet på principen i området **Principer och parametrar**. _Värdet_ kan anges till antingen Ange värde (hårdkodat för alla tilldelningar i initiativet) eller Använd initiativparametern (ställs in under varje initiativtilldelning). Om du väljer "Ange värde" kan du via listrutan till höger om _Värden_ ange eller välja värden. Om du väljer Använd initiativparametern så visas ett nytt avsnitt **Initiativparameter** där du kan definiera den parameter som ställs in under initiativtilldelningen. Tillåtna värden för den här initiativparametern kan ytterligare begränsa vad du kan ange under initiativtilldelningen.

   ![Change initiative definition parameters from allowed values](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > När det gäller vissa parametrar av typen `strongType` går det inte att automatiskt fastställa listan med värden. I de här fallen visas en ellips till höger om parameterraden. Selecting it opens the 'Parameter scope (&lt;parameter name&gt;)' page. På den här sidan väljer du den prenumeration som ska användas för att tillhandahålla värdealternativen. Det här parameterområdet används bara när initiativdefinitionen skapas. Den påverkar inte principutvärderingen eller initiativets omfattning efter tilldelningen.

1. Välj **Spara**.

### <a name="assign-an-initiative-definition"></a>Tilldela en initiativdefinition

1. Välj **Definitioner** under **Redigering** till vänster på sidan Azure Policy.

1. Leta upp initiativdefinitionen **Get Secure** som du skapade tidigare och markera den. Välj **Tilldela** överst på sidan för att öppna sidan **Bli säker: tilldela initiativ**.

   ![Assign a definition from Initiative definition page](../media/create-and-manage/assign-definition.png)

   You can also right-click on the selected row or select the ellipsis at the end of the row for a contextual menu. Välj sedan **Tilldela**.

   ![Alternative options for an initiative](../media/create-and-manage/select-right-click.png)

1. Fyll i sidan **Get Secure: Assign Initiative** genom att ange följande exempelinformation. Du kan använda din egen information.

   - Omfång: hanteringsgruppen eller prenumerationen som du sparade initiativet till blir standard.
     Du kan ändra omfånget för att tilldela initiativet till en prenumeration eller resursgrupp inom lagringsplatsen.
   - Undantag: Konfigurera några resurser inom omfånget för att förhindra att initiativtilldelningen tillämpas på dem.
   - Initiativdefinition och tilldelningsnamn: Get Secure (förifylld med namnet på initiativet som tilldelats).
   - Beskrivning: Initiativtilldelningen är skräddarsydd för att tillämpa den här gruppen med principdefinitioner.
   - Tilldelad av: anges automatiskt baserat på vem som är inloggad. Det här fältet är valfritt, så du kan ange anpassade värden.

1. Lämna **Skapa en hanterad identitet** avmarkerat. Den här rutan _måste_ markeras när den princip eller det initiativ som tilldelas omfattar en princip med effekten [deployIfNotExists](../concepts/effects.md#deployifnotexists). Eftersom den princip som används för den här självstudien inte gör det kan du lämna den tom. Mer information finns i avsnitten om [hanterade identiteter](../../../active-directory/managed-identities-azure-resources/overview.md) och [hur reparationssäkerhet fungerar](../how-to/remediate-resources.md#how-remediation-security-works).

1. Välj **Tilldela**.

## <a name="check-initial-compliance"></a>Kontrollera inledande efterlevnad

1. Välj **Efterlevnad** till vänster på Azure Policy-sidan.

1. Locate the **Get Secure** initiative. Det är sannolikt fortfarande i _Efterlevnadstillståndet_ **Inte startat**.
   Select the initiative to get full details on the progress of the assignment.

   ![Initiative compliance page - evaluations not started](../media/create-and-manage/compliance-status-not-started.png)

1. När initiativtilldelningen har slutförts, uppdateras sidan för efterlevnad med den _Efterlevnadstillståndet_ **Efterlever**.

   ![Initiative compliance page- resources compliant](../media/create-and-manage/compliance-status-compliant.png)

1. Selecting on any policy on the initiative compliance page opens the compliance details page for the policy. Den här sidan ger information på resursnivån för efterlevnad.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Undanta en icke-kompatibel eller nekad resurs med Exkludering

När du har angett att principdefinitionen kräver version 12.0 av SQL Server skulle en SQL-server som har skapats med en annan version än 12.0 nekas, precis som i exemplet ovan. I det här avsnittet går vi igenom hur du hanterar en nekad begäran att skapa en SQL-server genom att skapa ett undantag på en enskild resursgrupp. Undantaget förhindrar att principen (eller initiativet) tillämpas på resursen.
Följande exempel tillåter alla SQL-serverversioner i en enskild resursgrupp. Ett undantag kan gälla för en prenumeration, resursgrupp eller så kan du begränsa undantaget till enskilda resurser.

En distribution som har förhindrats av en tilldelad princip eller ett initiativ kan visas på två platser:

- On the resource group targeted by the deployment: Select **Deployments** in the left side of the page, then select the **Deployment Name** of the failed deployment. Resursen som nekades visas med statusen _Förbjuden_. To determine the policy or initiative and assignment that denied the resource, select **Failed. Click here for details ->** on the Deployment Overview page.
  Ett fönster öppnas till höger om sidan med felinformationen. Under **Felinformation** finns GUID för de relaterade principobjekten.

  ![Distributionen nekas av principtilldelningen](../media/create-and-manage/rg-deployment-denied.png)

- On the Azure Policy page: Select **Compliance** in the left side of the page and select the **Require SQL Server version 12.0** policy. På sidan som öppnas visas en ökning i antalet för **Neka**. På fliken **Händelser** visas även vem som försökte utföra den distribution som nekades av principen.

  ![Efterlevnadsöversikt för en tilldelad princip](../media/create-and-manage/compliance-overview.png)

I det här exemplet utförde Trent Baker, en av Contosos seniora virtualiseringsspecialister, obligatoriskt arbete. Vi behöver bevilja Trent ett undantag, men vi vill inte ha de SQL-servrar som inte är av version 12.0 i godtyckliga resursgrupper. Vi har skapat den nya resursgruppen **SQLServers_Excluded** och kommer nu att bevilja ett undantag till den för principtilldelningen.

### <a name="update-assignment-with-exclusion"></a>Uppdatera tilldelning med undantag

1. Välj **Tilldelningar** under **Redigering** till vänster om Azure Policy-sidan.

1. Bläddra igenom alla principtilldelningar och öppna tilldelningen *Kräv SQL Server version 12.0*.

1. Set the **Exclusion** by selecting the ellipsis and selecting the resource group to exclude, *SQLServers_Excluded* in this example.

   ![Add an excluded resource group to the policy assignment](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > Beroende på principen och dess effekt skulle undantaget också kunna beviljas till specifika resurser i en resursgrupp inom omfånget för tilldelningen. Eftersom **Neka** har använts i den här självstudien, gör det ingen skillnad att ange undantag för en specifik resurs som redan finns.

1. Select **Select** and then select **Save**.

I det här avsnittet löste du den nekade begäran genom att skapa ett undantag på en enskild resursgrupp.

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med att arbeta med resurser i den här självstudien kan du använda följande steg för att ta bort tilldelningar eller definitioner som skapades ovan:

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