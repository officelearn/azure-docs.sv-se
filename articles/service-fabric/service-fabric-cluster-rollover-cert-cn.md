---
title: Rulla över ett Azure Service Fabric-klustercertifikat
description: Lär dig hur du överför ett Service Fabric-klustercertifikat som identifieras med certifikatets gemensamma namn.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 94cc6841886b1b0eb4271ac0f727a2e3561e0081
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451965"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Rulla över ett klustercertifikat för service fabric manuellt
När ett service fabric-klustercertifikat är nära att löpa ut måste du uppdatera certifikatet.  Certifikatöverrullning är enkelt om [klustret har konfigurerats för att använda certifikat baserat på vanligt namn](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (i stället för tumavtryck).  Hämta ett nytt certifikat från en certifikatutfärdarmed ett nytt utgångsdatum.  Självsignerade certifikat stöder inte för kluster med produktionstjänst fabric, för att inkludera certifikat som genereras under arbetsflödet för att skapa Azure portal Cluster. Det nya certifikatet måste ha samma gemensamma namn som det äldre certifikatet. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Service Fabric-klustret använder automatiskt det deklarerade certifikatet ytterligare in i det framtida utgångsdatumet. när mer än ett verifiera certifikat är installerat på värden. En metod är att använda en Resource Manager-mall för att etablera Azure Resources. För icke-produktionsmiljö kan följande skript användas för att ladda upp ett nytt certifikat till ett nyckelvalv och sedan installera certifikatet på den virtuella datorns skalningsuppsättning: 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Beräknar Virtual Machine Scale Set Secrets stöder inte samma resurs-ID för två separata hemligheter, eftersom varje hemlighet är en version av unik resurs. 

## <a name="next-steps"></a>Efterföljande moment

* Lär dig mer om [klustersäkerhet](service-fabric-cluster-security.md).
* [Uppdatera och hantera klustercertifikat](service-fabric-cluster-security-update-certs-azure.md)
