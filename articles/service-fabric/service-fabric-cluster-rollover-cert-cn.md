---
title: Förnya ett certifikat för Azure Service Fabric-kluster | Microsoft Docs
description: Lär dig hur att förnya ett certifikat för Service Fabric-kluster identifieras av certifikatets unika namn.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi;aljo
ms.openlocfilehash: a471e1aa8b68e8b94094dc79c8ae71f24cf290cb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Distribuera manuellt via ett certifikat för Service Fabric-kluster
När ett certifikat för Service Fabric-klustret är väg att löpa ut, måste du uppdatera certifikatet.  Förnya certifikatet är enkel om klustret var [till använder certifikat baserat på nätverksnamn](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (i stället för tumavtrycket).  Hämta ett nytt certifikat från en certifikatutfärdare med ett nytt utgångsdatum.  Självsignerade certifikat, inklusive de som skapas när du distribuerar ett Service Fabric-kluster i Azure portal stöds inte.  Det nya certifikatet måste ha samma allmänna namn som det tidigare certifikatet. 

Följande skript Överför ett nytt certifikat till ett nyckelvalv och installerar certifikatet på virtuella datorns skaluppsättning.  Service Fabric-klustret ska använda automatiskt certifikatet med det senaste förfallodatumet.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

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

$certConfig = New-AzureRmVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzureRmVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzureRmVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

Mer information finns på följande:
* Lär dig mer om [kluster säkerhet](service-fabric-cluster-security.md).
* [Uppdatera och hantera klustercertifikat](service-fabric-cluster-security-update-certs-azure.md)

