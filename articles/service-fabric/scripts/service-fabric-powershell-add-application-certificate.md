---
title: Lägga till programcert i ett kluster i Powershell
description: Skriptexempel för Azure PowerShell – Lägga till ett programcertifikat till ett Service Fabric-kluster.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 61c22a3949008d61bbe3472f601d2d0dd597a0ac
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77114339"
---
# <a name="add-an-application-certificate-to-a-service-fabric-cluster"></a>Distribuera ett programcertifikat till ett Service Fabric-kluster

Det här exempelskriptet går igenom hur du skapar ett certifikat i Key Vault och distribuerar det sedan till en av skalningsuppsättningarna för den virtuella datorn. Det här scenariot använder inte Service Fabric direkt, utan beror snarare på Key Vault och på skaluppsättningar för virtuella datorer.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i [Azure PowerShell-guiden](/powershell/azure/overview) och kör sedan `Connect-AzAccount` för att skapa en anslutning med Azure. 

## <a name="create-a-certificate-in-key-vault"></a>Skapa ett certifikat i Key Vault

```powershell
$VaultName = ""
$CertName = ""
$SubjectName = "CN="

$policy = New-AzKeyVaultCertificatePolicy -SubjectName $SubjectName -IssuerName Self -ValidityInMonths 12
Add-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName -CertificatePolicy $policy
```

## <a name="or-upload-an-existing-certificate-into-key-vault"></a>Eller ladda upp ett befintligt certifikat till Key Vault

```powershell
$VaultName= ""
$CertName= ""
$CertPassword= ""
$PathToPFX= ""

$Cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $PathToPFX, $CertPassword

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)
$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $CertPassword
   } | ConvertTo-Json
$contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
$content = [System.Convert]::ToBase64String($contentbytes)

$SecretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

# Upload the certificate to the key vault as a secret
$Secret = Set-AzKeyVaultSecret -VaultName $VaultName -Name $CertName -SecretValue $SecretValue

```

## <a name="update-virtual-machine-scale-sets-profile-with-certificate"></a>Uppdatera skalningsuppsättningar för virtuella datorer med certifikat

```powershell
$ResourceGroupName = ""
$VMSSName = ""
$CertStore = "My" # Update this with the store you want your certificate placed in, this is LocalMachine\My

$CertConfig = New-AzVmssVaultCertificateConfig -CertificateUrl (Get-AzKeyVaultCertificate -VaultName $VaultName -Name $CertName).SecretId -CertificateStore $CertStore
$VMSS = Get-AzVmss -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMSSName

# If this KeyVault is already known by the virtual machine scale set, for example if the cluster certificate is deployed from this keyvault, use
$VMSS.virtualmachineprofile.osProfile.secrets[0].vaultCertificates.Add($certConfig)

# Otherwise use
$VMSS = Add-AzVmssSecret -VirtualMachineScaleSet $VMSS -SourceVaultId (Get-AzKeyVault -VaultName $VaultName).ResourceId  -VaultCertificate $CertConfig
```

## <a name="update-the-virtual-machine-scale-set"></a>Uppdatera skalningsuppsättningen för den virtuella datorn
```powershell
Update-AzVmss -ResourceGroupName $ResourceGroupName -VirtualMachineScaleSet $VMSS -VMScaleSetName $VMSSName
```

> Om du vill att certifikatet ska placeras på flera nodtyper i klustret bör den andra och den tredje delen av skriptet upprepas för varje nodtyp som ska ha certifikatet.

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon: varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Ny-AzKeyVaultCertificatePolicy](/powershell/module/az.keyvault/New-AzKeyVaultCertificatePolicy) | Skapar en princip i minnet som representerar certifikatet |
| [Tillägg-AzKeyVaultCertificate](/powershell/module/az.keyvault/Add-AzKeyVaultCertificate)| Distribuerar principen till Key Vault |
| [Nya-AzVmssVaultCertificateConfig](/powershell/module/az.compute/New-AzVmssVaultCertificateConfig) | Skapar en konfiguration i minnet som representerar certifikatet i en virtuell dator |
| [Get-AzVmss](/powershell/module/az.compute/Get-AzVmss) |  |
| [Tillägg-AzVmssSecret](/powershell/module/az.compute/Add-AzVmssSecret) | Lägger till certifikatet i minnesdefinitionen för skalningsuppsättningen för den virtuella datorn |
| [Update-AzVmss](/powershell/module/az.compute/Update-AzVmss) | Distribuerar den nya definitionen av skaluppsättningen för den virtuella datorn |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure Service Fabric finns i [Azure PowerShell-exemplen](../service-fabric-powershell-samples.md).
