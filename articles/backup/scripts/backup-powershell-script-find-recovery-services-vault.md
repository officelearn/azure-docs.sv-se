---
title: PowerShell Script - hitta Arkiv för lagringskonto
description: Lär dig hur du använder ett Azure PowerShell-skript för att hitta återställningstjänstvalvet där ditt lagringskonto är registrerat.
ms.topic: sample
ms.date: 1/28/2020
ms.openlocfilehash: 786420ec8cef6516f7261c71b40641693efece07
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "76775363"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>Powershell Script för att hitta Recovery Services Vault där ett lagringskonto är registrerat

Det här skriptet hjälper dig att hitta valvet för återställningstjänster där ditt lagringskonto är registrerat.

## <a name="sample-script"></a>Exempelskript

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>Så här kör du skriptet

1. Spara skriptet ovan på din maskin med ett namn som du väljer. I det här exemplet sparade vi den som *FindRegisteredStorageAccount.ps1*.
2. Kör skriptet genom att ange följande parametrar:

    * **-ResourceGroupName** - Resursgruppen för lagringskontot
    * **-StorageAccountName** - Namn på lagringskonto
    * **-SubscriptionID** - ID för prenumeration där lagringskontot finns.

I följande exempel försöker du hitta valvet för återställningstjänster där *kontot för afsaccount-lagring* är registrerat:

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Resultat

Utdata visar den fullständiga sökvägen till återställningstjänstvalvet där lagringskontot är registrerat. Här är exempel på utdata:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [säkerhetskopierar Azure File Shares från Azure-portalen](https://docs.microsoft.com/azure/backup/backup-afs)
