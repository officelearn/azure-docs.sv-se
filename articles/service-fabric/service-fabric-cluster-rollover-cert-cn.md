---
title: Förnya ett Azure Service Fabric-kluster certifikat | Microsoft Docs
description: Lär dig hur du förnyar ett Service Fabric kluster certifikat som identifieras av certifikatets egna namn.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: atsenthi
ms.openlocfilehash: 5d11054ca8eb684f1f25a25ddeac1b53e82b3775
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599913"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Manuellt återställa ett Service Fabric kluster certifikat
När ett Service Fabric kluster certifikat snart går ut måste du uppdatera certifikatet.  Certifikat förnyelse är enkelt om klustret har [kon figurer ATS för att använda certifikat baserat på eget namn](service-fabric-cluster-change-cert-thumbprint-to-cn.md) (i stället för tumavtryck).  Hämta ett nytt certifikat från en certifikat utfärdare med ett nytt förfallo datum.  Självsignerade certifikat har inte stöd för produktions Service Fabric kluster, för att inkludera certifikat som genereras under Azure Portal kluster skapande av arbets flöde. Det nya certifikatet måste ha samma egna namn som det äldre certifikatet. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Service Fabric klustret använder automatiskt det deklarerade certifikatet med längre fram till framtida förfallo datum. När mer än ett verifierat certifikat är installerat på värden. Ett bra tips är att använda en Resource Manager-mall för att etablera Azure-resurser. I icke-produktions miljö kan följande skript användas för att ladda upp ett nytt certifikat till ett nyckel valv och installerar sedan certifikatet på den virtuella datorns skal uppsättning: 

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

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Beräknar att hemligheter för skalnings uppsättningar för virtuella datorer inte stöder samma resurs-ID för två separata hemligheter, eftersom varje hemlighet är en versions unik resurs. 

Läs följande om du vill veta mer:
* Lär dig mer om [kluster säkerhet](service-fabric-cluster-security.md).
* [Uppdatera och hantera kluster certifikat](service-fabric-cluster-security-update-certs-azure.md)

