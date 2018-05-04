---
title: Använda Azure Policy för att skapa och hantera principer för att använda organisatorisk efterlevnad | Microsoft Docs
description: Använd Azure Policy för att genomdriva standarder, efterleva krav på regelefterlevnad och granskning, kontrollera kostnader, behålla en konsekvent säkerhet och prestanda och tillämpa företagsomfattande principer.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/19/2018
ms.topic: tutorial
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 0b6e77d080ce4e4483709b9a5e47dca21c22e1d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>Skapa och hantera principer för att använda kompatibilitet

Det är viktigt att du förstår hur du skapar och hanterar principer i Azure för att du ska fortsätta att efterleva företagets standarder och servicenivåavtal. I den här självstudien får du lära dig att använda Azure Policy för att utföra några av de vanligaste uppgifterna som handlar om att skapa, tilldela och hantera principer i organisationen, som:

> [!div class="checklist"]
> * Tilldela en princip för att genomdriva ett villkor för resurser som du skapar i framtiden
> * Skapa och tilldela en initiativdefinition för att spåra efterlevnad för flera resurser
> * Lösa en icke-kompatibel eller nekad resurs
> * Implementera en ny princip i en organisation

Om du vill tilldela en princip för att identifiera dina befintliga resursers efterlevnadstillstånd går artiklarna i snabbstarten igenom det. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="assign-a-policy"></a>Tilldela en princip

Det första steget för att tillämpa efterlevnad av Azure Policy är att tilldela en principdefinition. En principdefinition anger under vilket villkor en princip tillämpas och vilken åtgärd som ska vidtas. I det här exemplet ska du tilldela en inbyggd principdefinition som heter *Kräv SQL Server Version 12.0* för att genomdriva villkoret om att alla SQL Server-databaser måste vara v12.0 för att vara kompatibla.

1. Starta Azure Policy-tjänsten i Azure-portalen genom att söka efter och välja **Princip** i fönstret till vänster.

   ![Sök efter princip](media/assign-policy-definition/search-policy.png)

2. Välj **Tilldelningar** i det vänstra fönstret på sidan för Azure Policy. En tilldelning är en princip som tilldelats ett specifikt område.
3. Välj **Tilldela princip** längst upp på sidan **Tilldelningar**.

   ![Tilldela en principdefinition](media/create-manage-policy/select-assign-policy.png)

4. På sidan **Tilldela princip** klickar du på knappen ![Principdefinition](media/assign-policy-definition/definitions-button.png) bredvid fältet **Princip** för att öppna listan med tillgängliga definitioner. Du kan filtrera principdefinitionen **Typ** på *BuiltIn* om du vill visa alla och läsa deras beskrivningar.

   ![Öppna tillgängliga principdefinitioner](media/create-manage-policy/open-policy-definitions.png)

5. Välj **Kräv SQL Server Version 12.0**. Om du inte hittar det genast skriver du **Kräv SQL Server Version 12.0** i sökrutan och trycker sedan på RETUR.

   ![Hitta en princip](media/create-manage-policy/select-available-definition.png)

6. Det visade **namnet** fylls i automatiskt, men du kan ändra det. I det här exemplet ska du använda *Kräv SQL Server version 12.0*. Du kan också lägga till en valfri **Beskrivning**. Beskrivningen innehåller information om hur principtilldelningen garanterar att alla SQL-servrar som skapas i den här miljön är i version 12.0.

7. Ändra prisnivån till **Standard** för att se till att principen används på befintliga resurser.

   Det finns två prisnivåer i Azure Policy – *Kostnadsfri* och *Standard*. Med den kostnadsfria nivån kan du bara tillämpa principer på framtida resurser, medan Standard gör det möjligt att även tillämpa dem på befintliga resurser för att förstå kompatibilitetsstatusen bättre. Eftersom Azure Policy är i förhandsversion har vi ännu inte lanserat någon prissättningsmodell, så du debiteras inte om du väljer *Standard*. Mer information om prissättning finns i [priser för Azure Policy](https://azure.microsoft.com/pricing/details/azure-policy).

8. Välj **omfånget** – prenumerationen (eller resursgruppen) du registrerade tidigare. En omfattning avgör vilka resurser eller grupper med resurser som principtilldelningen används på. Det kan vara allt från en prenumeration till resursgrupper.

   I det här exemplet används prenumerationen **Azure Analytics Capacity Dev**. Din prenumeration skiljer sig.

10. Välj **Tilldela**.

## <a name="implement-a-new-custom-policy"></a>Implementera en ny anpassad princip

Nu när du har tilldelat en inbyggd principdefinition kan du göra mer med Azure Policy. Sedan ska du skapa en ny anpassad princip för att spara kostnader genom att se till att de virtuella datorer du har skapat i din miljö inte kan vara i G-serien. På så sätt nekas varje gång en begäran från en användare i organisationen som försöker skapa en virtuell dator i G-serien.

1. Välj **Definition** under **Redigering** i fönstret till vänster.

   ![Definition under redigering](media/create-manage-policy/definition-under-authoring.png)

2. Välj **+ Principdefinition**.
3. Ange följande:

   - Namnet på principdefinitionen – *Kräv VM SKU:er som är mindre än G-serien*
   - Beskrivningen av vad principdefinitionen är avsedd att göra – Den här principdefinitionen tvingar alla virtuella datorer som är skapade i det här omfånget att ha SKU:er som är mindre än G-serien för att minska kostnader.
   - Prenumerationen som principdefinitionen finns i. I det här fallet finns principdefinitionen i **Advisor Analytics Capacity Dev**. Din prenumerationslista är annorlunda.
   - Välj bland befintliga alternativ eller skapa en ny kategori för definitionen av den här principen.
   - Kopiera följande json-kod och uppdatera den sedan efter dina behov med:
      - Principparametrarna.
      - Principens regler/villkor, i det här fallet – VM SKU-storlek som motsvarar G-serien
      - Principens effekt, i det här fallet – **Neka**.

    Så här ska din json se ut. Klistra in den reviderade koden i Azure-portalen.

    ```json
{
    "policyRule": {
      "if": {
        "allOf": [
          {
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

    Värdet i *fältegenskapen* i principregeln måste vara något av följande: Namn, Typ, Plats, Taggar eller ett alias. Till exempel `"Microsoft.Compute/VirtualMachines/Size"`.

    Om du vill se fler exempel på json-kod läser du artikeln om [mallar för Azure Policy](json-samples.md).

4. Välj **Spara**.

## <a name="create-a-policy-definition-with-rest-api"></a>Skapa en principdefinition med REST API

Du kan skapa en princip med REST API för principdefinitioner. Med REST API kan du skapa och ta bort principdefinitioner och få information om befintliga definitioner.
Om du vill skapa en principdefinition använder du följande exempel:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

```
Inkludera en begärantext som liknar följande exempel:

```
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

Innan du fortsätter med PowerShell-exemplet ska du se till att du har den senaste versionen av Azure PowerShell installerad. Principparametrar lades till i version 3.6.0. Om du har en tidigare version returnerar exemplen ett fel som anger att parametern inte går att hitta.

Du kan skapa en principdefinition med cmdleten `New-AzureRmPolicyDefinition`.

Om du vill skapa en principdefinition från en fil skickar du sökvägen till filen. Använd följande exempel för en extern fil:

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -DisplayName "Deny cool access tiering for storage" `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Använd följande exempel för en lokal fil:

```
$definition = New-AzureRmPolicyDefinition `
    -Name denyCoolTiering `
    -Description "Deny cool access tiering for storage" `
    -Policy "c:\policies\coolAccessTier.json"
```

Om du vill skapa en principdefinition med en infogad regel använder du följande exempel:

```
$definition = New-AzureRmPolicyDefinition -Name denyCoolTiering -Description "Deny cool access tiering for storage" -Policy '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

Utdata lagras i ett `$definition`-objekt som används under principtilldelningen.
I följande exempel skapas en principdefinition som innehåller parametrarna:

```
$policy = '{
    "if": {
        "allOf": [
            {
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

$definition = New-AzureRmPolicyDefinition -Name storageLocations -Description "Policy to specify locations for storage accounts." -Policy $policy -Parameter $parameters
```

## <a name="view-policy-definitions"></a>Visa principdefinitioner

Om du vill visa alla principdefinitioner i din prenumeration använder du följande kommando:

```
Get-AzureRmPolicyDefinition
```

Den returnerar alla tillgängliga principdefinitioner, inklusive inbyggda principer. Varje princip returneras i följande format:

```
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

Du kan skapa en principdefinition med Azure CLI med kommandot för principdefinition.
Om du vill skapa en principdefinition med en infogad regel använder du följande exempel:

```
az policy definition create --name denyCoolTiering --description "Deny cool access tiering for storage" --rules '{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "field": "kind",
        "equals": "BlobStorage"
      },
      {
        "not": {
          "field": "Microsoft.Storage/storageAccounts/accessTier",
          "equals": "cool"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}'
```

## <a name="view-policy-definitions"></a>Visa principdefinitioner

Om du vill visa alla principdefinitioner i din prenumeration använder du följande kommando:

```
az policy definition list
```

Den returnerar alla tillgängliga principdefinitioner, inklusive inbyggda principer. Varje princip returneras i följande format:

```
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

Med en initiativdefinition kan du gruppera flera principdefinitioner för att uppnå ett övergripande mål. Du skapar en initiativdefinition för att se till att resurser inom definitionens omfång efterlever principdefinitionerna som utgör initiativdefinitionen.  Se [översikten över Azure Policy](./azure-policy-introduction.md) för mer information om initiativdefinitioner.

### <a name="create-an-initiative-definition"></a>Skapa en initiativdefinition

1. Välj **Definitioner** under **Redigering** i fönstret till vänster.

   ![Välja definitioner](media/create-manage-policy/select-definitions.png)

2. Välj **Initiativdefinition** överst på sidan. Då kommer du till formuläret **Initiative Definition**.
3. Ange initiativets namn och beskrivning.

   I det här exemplet ska du se till att resurser efterlever principdefinitioner om säkerhet. Så namnet på initiativet ska vara **Get Secure**, och beskrivningen ska vara: **This initiative has been created to handle all policy definitions associated with securing resources** (Det här initiativet har skapats för att hantera alla principdefinitioner som är associerade med att skydda resurser).

   ![Initiativdefinition](media/create-manage-policy/initiative-definition.png)

4. Bläddra igeno listan med **tillgängliga definitioner** och välj principdefinitionerna du vill lägga till för initiativet. **Lägg till** följande inbyggda principdefinitioner för initiativet **Get secure**:
   - Kräv SQL Server version 12.0
   - Övervaka oskyddade webbappar i Security Center.
   - Övervaka tillåtande nätverk i Security Center.
   - Övervaka lista över möjliga tillåtna appar i Security Center.
   - Övervaka okrypterade VM-diskar i Security Center.

   ![Initiativdefinitioner](media/create-manage-policy/initiative-definition-2.png)

   När du har valt principdefinitioner i listan ser du dem under **Principer och parametrar** som visas på föregående bild.

5. Använd **Definitionens plats** för att välja en prenumeration för att lagra definitionen. Välj **Spara**.

### <a name="assign-an-initiative-definition"></a>Tilldela en initiativdefinition

1. Gå till fliken **Definitioner** under **Redigering**.
2. Sök efter initiativdefinitionen **Get secure** som du skapade.
3. Välj initiativdefinitionen och välj sedan **Tilldela**.

   ![Tilldela en definition](media/create-manage-policy/assign-definition.png)

4. Fyll i formuläret **Tilldelning** genom att ange följande exempelinformation. Du kan använda din egen information.
   - Namn: Get secure-tilldelning
   - Beskrivning: Den här initiativtilldelningen är skräddarsydd för att framtvinga den här gruppen med principdefinitioner i prenumerationen **Azure Advisor Capacity Dev**.
   - Prisnivå: Standard
   - Undersök var du vill att tilldelningen ska användas: **Azure Advisor Capacity Dev**. Du kan välja din egen prenumeration och resursgrupp.

5. Välj **Tilldela**.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Undanta en icke-kompatibel eller nekad resurs med Exkludering

När du har angett att principdefinitionen kräver version 12.0 av SQL Server skulle en SQL-server som har skapats med en annan version än 12.0 nekas, precis som i exemplet ovan. I det här avsnittet går du igenom hur du löser ett nekat försök till att skapa en SQL-server genom att begära en exkludering av vissa resurser. Undantaget förhindrar principtillämpning. I följande exempel tillåts valfri version av SQL-servern. Ett undantag kan tillämpas på en resursgrupp eller så kan du begränsa undantaget till enskilda resurser.

1. Välj **Tilldelningar** i det vänstra fönstret.
2. Bläddra igenom alla principtilldelningar och öppna tilldelningen *Kräv SQL Server version 12.0*.
3. **Välj** ett undantag för resurser i resursgrupperna där du försöker skapa SQL-servern. I det här exemplet ska du exkludera Microsoft.Sql/servers/databases: *azuremetrictest/testdb* och *azuremetrictest/testdb2*.

   ![Begär exkludering](media/create-manage-policy/request-exclusion.png)

   Andra sätt du kan lösa en nekad resurs på är: Hör av dig till kontakten som är associerad med principen om du har en stark motivering för att du behöver skapa SQL-servern, och redigera principen direkt om du har åtkomst till den.

4. Klicka på **Tilldela**.

I det här avsnittet har du löst nekandet av ditt försök att skapa en SQL-server genom att begära en exkludering av resurserna.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med efterföljande självstudier ska du inte rensa upp resurserna som du skapade i den här guiden. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla tilldelningar eller definitioner som du skapade ovan:

1. Välj **Definitioner** (eller **Tilldelningar** om du försöker ta bort en uppgift) i fönstret till vänster.
2. Sök efter den nya initiativ- eller principdefinitionen (eller uppgiften) du nyss skapade.
3. Välj ellipserna i slutet av definitionen eller uppgiften och välj **Ta bort definition** (eller **Ta bort tilldelning**).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Tilldelat en princip för att genomdriva ett villkor för resurser som du skapar i framtiden
> * Skapat och tilldelat en initiativdefinition för att spåra efterlevnad för flera resurser
> * Löst en icke-kompatibel eller nekad resurs
> * Implementerat en ny princip i en organisation

Mer information om principdefinitionernas strukturer finns i den här artikeln:

> [!div class="nextstepaction"]
> [Azure Policy-definitionsstruktur](policy-definition.md)
