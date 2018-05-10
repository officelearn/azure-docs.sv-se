---
title: Skapa principer och visa kompatibilitetsdata med Azure princip programmässigt
description: Den här artikeln vägleder dig genom programmässigt skapa och hantera principer för Azure-principen.
services: azure-policy
keywords: ''
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-policy
manager: carmonm
ms.custom: ''
ms.openlocfilehash: 5737c33fc4c139e3b0a5535d371ef7cc1d11b9e6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="programmatically-create-policies-and-view-compliance-data"></a>Skapa principer och visa kompatibilitetsdata programmässigt

Den här artikeln vägleder dig genom att skapa och hantera principer programmässigt. Den visar hur du visar kompatibilitetstillstånd för resursen och principer. Principdefinitioner tillämpa olika regler och effekter över dina resurser. Tvingande säkerställer att resurser vara kompatibla med företagets standarder och servicenivåavtal.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att följande krav är uppfyllda:

1. Installera om du inte redan gjort det [ARMClient](https://github.com/projectkudu/ARMClient). Det är ett verktyg som skickar HTTP-förfrågningar till Azure Resource Manager-baserade API: er.
2. Uppdatera din AzureRM PowerShell-modul till den senaste versionen. Mer information om den senaste versionen finns [Azure PowerShell](https://github.com/Azure/azure-powershell/releases).
3. Registrera principen insikter resursprovidern med Azure PowerShell för att säkerställa att din prenumeration fungerar med resursprovidern. När du ska registrera en resursleverantör måste du ha behörighet att utföra registeringsåtgärden för resursprovidern. Den här åtgärden ingår i rollerna Deltagare och Ägare. Registrera resursprovidern genom att köra följande kommando:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Läs mer om registrering och visa resursproviders [Resursproviders och typer](../azure-resource-manager/resource-manager-supported-services.md).
4. Om du inte redan gjort installera Azure CLI. Du kan hämta den senaste versionen på [installera Azure CLI 2.0 på Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Skapa och tilldela en principdefinition

Första steget mot bättre synlighet för dina resurser är att skapa och tilldela principer över dina resurser. Nästa steg är att lära dig hur du skapar och tilldelar du en princip programmässigt. Exempel principen granskningar storage-konton är tillgängliga för alla offentliga nätverk med PowerShell, Azure CLI och HTTP-begäranden.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Skapa och tilldela en principdefinition med PowerShell

1. Använd följande JSON-fragment för att skapa en JSON-fil med namnet AuditStorageAccounts.json.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

  Läs mer om att redigera en principdefinition [Azure Definition principstruktur](policy-definition.md).
2. Kör följande kommando för att skapa en principdefinition med hjälp av filen AuditStorageAccounts.json.

  ```azurepowershell-interactive
  New-AzureRmPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
  ```

  Kommandot skapar en principdefinition med namnet _Audit Storage-konton öppna till offentliga nätverk_. Mer information om andra parametrar som du kan använda finns [ny AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition).
3. När du har skapat din principdefinitionen kan du skapa en principtilldelning genom att köra följande kommandon:

  ```azurepowershell-interactive
  $rg = Get-AzureRmResourceGroup -Name 'ContosoRG'
  $Policy = Get-AzureRmPolicyDefinition -Name 'AuditStorageAccounts'
  New-AzureRmPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
  ```

  Ersätt _ContosoRG_ med namnet på din avsedda resursgrupp.

Mer information om hur du hanterar principer för företagsresurser med hjälp av Azure Resource Manager PowerShell-modulen finns [AzureRM.Resources](/powershell/module/azurerm.resources/#policies).

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Skapa och tilldela en principdefinition med ARMClient

Använd följande procedur för att skapa en principdefinition.

1. Kopiera följande JSON-fragment för att skapa en JSON-fil. Du måste anropa filen i nästa steg.

  ```json
  "properties": {
      "displayName": "Audit Storage Accounts Open to Public Networks",
      "policyType": "Custom",
      "mode": "Indexed",
      "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
      "parameters": {},
      "policyRule": {
          "if": {
              "allOf": [{
                      "field": "type",
                      "equals": "Microsoft.Storage/storageAccounts"
                  },
                  {
                      "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                      "equals": "Allow"
                  }
              ]
          },
          "then": {
              "effect": "audit"
          }
      }
  }
  ```

2. Skapa principdefinitionen med hjälp av följande anrop:

  ```
  armclient PUT "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
  ```

  Ersätt den föregående &lt;subscriptionId&gt; med ID för prenumerationen avsedda.

Mer information om strukturen i frågan finns [Principdefinitioner – skapa eller uppdatera](/rest/api/resources/policydefinitions/createorupdate).

Använd följande procedur för att skapa en principtilldelning och tilldela principdefinitionen på resursgruppsnivå.

1. Kopiera följande JSON-fragment för att skapa en tilldelning principfil JSON. Ersätt exempelinformationen i &lt; &gt; symboler med egna värden.

  ```json
  {
      "properties": {
          "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
          "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
          "parameters": {},
          "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
          "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
      }
  }
  ```

2. Skapa principtilldelningen med hjälp av följande anrop:

  ```
  armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
  ```

  Ersätt exempelinformationen i &lt; &gt; symboler med egna värden.

  Mer information om HTTP-anrop till REST-API finns [Azure REST API-resurser](/rest/api/resources/).

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Skapa och tilldela en principdefinition med Azure CLI

Använd följande procedur för att skapa en principdefinition:

1. Kopiera följande JSON-fragment för att skapa en tilldelning principfil JSON.

  ```json
  {
      "if": {
          "allOf": [{
                  "field": "type",
                  "equals": "Microsoft.Storage/storageAccounts"
              },
              {
                  "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                  "equals": "Allow"
              }
          ]
      },
      "then": {
          "effect": "audit"
      }
  }
  ```

2. Kör följande kommando för att skapa en principdefinition:

  ```azurecli-interactive
az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
  ```

3. Använd följande kommando för att skapa en principtilldelning. Ersätt exempelinformationen i &lt; &gt; symboler med egna värden.

  ```azurecli-interactive
  az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
  ```

Du kan få principen Definitions-ID med hjälp av PowerShell med följande kommando:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

Princip-ID för definition för principdefinitionen som du skapade bör likna följande exempel:

```
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Mer information om hur du kan hantera resursprinciper med Azure CLI finns [Azure CLI resursprinciper](/cli/azure/policy?view=azure-cli-latest).

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

En resurs är icke-kompatibla om du inte följer principen eller initiativ regler för en tilldelning. Följande tabell visar hur olika principer effekter som fungerar med villkoret utvärderingsversionen för resulterande kompatibilitetsstatus:

| Resource tillstånd | Verkan | Utvärderingen | Kompatibilitetsstatus |
| --- | --- | --- | --- |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Icke-kompatibel |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Kompatibel |
| Ny | Audit, AuditIfNotExist\* | True | Icke-kompatibel |
| Ny | Audit, AuditIfNotExist\* | False | Kompatibel |

\* Lägg till, DeployIfNotExist och AuditIfNotExist effekterna kräver instruktionen om ska TRUE. Effekterna kräver även villkoret förekomsten ska vara FALSE för icke-kompatibla. När det är TRUE utlöser IF-villkoret utvärdering av villkoret Finns för de relaterade resurserna.

För att bättre förstå hur resurser har flaggats som icke-kompatibla, ska vi använda princip tilldelningen exemplet skapade ovan.

Anta att du har en resursgrupp – ContsoRG, med vissa storage-konton (markerat i rött) som exponeras för offentliga nätverk.

![Storage-konton som exponeras för offentliga nätverk](media/policy-insights/resource-group01.png)

I det här exemplet behöver du vara försiktig med säkerhetsrisker. Nu när du har skapat en principtilldelning, utvärderas den för alla lagringskonton i resursgruppen ContosoRG. Den granskningar tre icke-kompatibla lagringskonton, därför ändra deras tillstånd att **icke-kompatibla.**

![Granskas icke-kompatibla storage-konton](media/policy-insights/resource-group03.png)

Använd följande procedur för att identifiera resurser i en resursgrupp som inte är kompatibla med principtilldelningen. I det här exemplet är resurserna storage-konton i resursgruppen ContosoRG.

1. Hämta princip tilldelnings-ID genom att köra följande kommandon:

  ```azurepowershell-interactive
  $policyAssignment = Get-AzureRmPolicyAssignment | Where-Object { $_.Properties.displayName -eq 'Audit Storage Accounts with Open Public Networks' }
  $policyAssignment.PolicyAssignmentId
  ```

  Mer information om hur du får en principtilldelning ID finns [Get-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/Get-AzureRmPolicyAssignment).

2. Kör följande kommando för att ha resurs-ID: N av icke-kompatibla resurser kopieras till en JSON-fil:

  ```
  armclient POST "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
  ```

3. Resultatet bör likna följande exempel:

  ```json
  {
      "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
      "@odata.count": 3,
      "value": [{
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount1Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount2Id>"
          },
          {
              "@odata.id": null,
              "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
              "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.storage/storageaccounts/<storageaccount3ID>"
          }
      ]
  }
  ```

Resultatet är likvärdiga med vad som skulle normalt visas i listan under **icke-kompatibel resurser** i den [Azure portal-vy](assign-policy-definition.md#identify-non-compliant-resources).

Icke-kompatibla resurser för närvarande endast identifieras med hjälp av Azure portal och med HTTP-begäranden. Mer information om frågor princip tillstånd finns det [Principstatus](/rest/api/policy-insights/policystates) artikel för API-referens.

## <a name="view-policy-events"></a>Visa händelser för principen

När en resurs skapas eller uppdateras, skapas en utvärderingsresultat av principen. Resultaten kallas _Principhändelser_. Kör följande fråga om du vill visa alla Principhändelser som är associerade med principtilldelningen.

```
armclient POST "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2017-12-12-preview"
```

Ditt resultat liknar följande exempel:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 3
    }]
}
```

Du kan bara visa Principhändelser med HTTP-begäranden som principen tillstånd. Mer information om frågor Principhändelser finns i [Principhändelser](/rest/api/policy-insights/policyevents) referensartikeln.

## <a name="next-steps"></a>Nästa steg

Granska följande artiklar för mer information om kommandon och frågor i den här artikeln.

- [Azure REST API-resurser](/rest/api/resources/)
- [Azure RM PowerShell-moduler](/powershell/module/azurerm.resources/#policies)
- [Azure CLI-kommandona för principen](/cli/azure/policy?view=azure-cli-latest)
- [Principprovidern insikter resurs REST API-referens](/rest/api/policy-insights)