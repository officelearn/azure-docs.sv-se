---
title: HPC Pack 2016 kluster i Azure | Microsoft Docs
description: "Lär dig att distribuera ett HPC Pack 2016-kluster i Azure"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 3dde6a68-e4a6-4054-8b67-d6a90fdc5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 03/09/2018
ms.author: danlep
ms.openlocfilehash: c26dd85d896445e19efb9906d953fd535fc1fb5c
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-an-hpc-pack-2016-cluster-in-azure"></a>Distribuera ett HPC Pack 2016-kluster i Azure

Följ stegen i den här artikeln för att distribuera en [uppdatering 1 för Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) kluster i Azure-datorer. HPC Pack är Microsofts ledigt HPC-lösning baserat på Microsoft Azure och Windows Server-teknik och stöder en bred HPC-arbetsbelastning.

Använd någon av de [Azure Resource Manager-mallar](https://github.com/MsHpcPack/HPCPack2016) att distribuera HPC Pack 2016-klustret. Du har flera val av klustertopologi med olika antal och typer av klustrets huvudnoder och compute-noder.

## <a name="prerequisites"></a>Förutsättningar

### <a name="pfx-certificate"></a>PFX-certifikat

Ett kluster för Microsoft HPC Pack 2016 kräver ett Personal Information Exchange (PFX)-certifikat för säker kommunikation mellan HPC-noder. Certifikatet måste uppfylla följande krav:

* Den måste ha en privat nyckel kapacitet för nyckelutbyte
* Nyckelanvändning innehåller digitala signatur och nyckelchiffrering
* Utökad nyckelanvändning innehåller klientautentisering och serverautentisering

Om du inte redan har ett certifikat som uppfyller dessa krav, kan du begära certifikatet från en certifikatutfärdare. Du kan också använda följande kommandon för att generera ett självsignerat certifikat baserat på operativsystemet som du kör kommandot. Exportera sedan certifikatet som en lösenordsskyddad PFX-fil med privat nyckel.

* **För Windows 10 eller Windows Server 2016**, kör du inbyggt **ny SelfSignedCertificate** PowerShell-cmdlet enligt följande:

  ```PowerShell
  New-SelfSignedCertificate -Subject "CN=HPC Pack 2016 Communication" -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2") -CertStoreLocation cert:\CurrentUser\My -KeyExportPolicy Exportable -NotAfter (Get-Date).AddYears(5)
  ```
* **För operativsystem tidigare än Windows 10 eller Windows Server 2016**, ladda ned den [självsignerat certifikat generator](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) från Microsoft Script Center. Extrahera innehållet och kör följande kommandon i PowerShell-Kommandotolken:

    ```PowerShell 
    Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
    New-SelfSignedCertificateEx -Subject "CN=HPC Pack 2016 Communication" -KeySpec Exchange -KeyUsage "DigitalSignature,KeyEncipherment" -EnhancedKeyUsage "Server Authentication","Client Authentication" -StoreLocation CurrentUser -Exportable -NotAfter (Get-Date).AddYears(5)
    ```

När certifikatet har skapats i arkivet för aktuell användare använder du snapin-modulen certifikat för att exportera certifikatet som en lösenordsskyddad PFX-fil med privat nyckel. Du kan också exportera certifikat med hjälp av den [Export Pfxcertificate](/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps) PowerShell-cmdlet.

### <a name="upload-certificate-to-an-azure-key-vault"></a>Överför certifikatet till en Azure key vault

Innan du distribuerar HPC-kluster, ladda upp PFX-certifikat till ett [Azure key vault](../../key-vault/index.md) som en hemlighet och registrera följande information för användning under distributionen: **valvnamnet**, **valvet resursgruppen**, **Certifikatwebbadress**, och **certifikattumavtrycket**.

Ett exempel PowerShell-skript för att ladda upp certifikatet, skapa nyckelvalvet och generera informationen som krävs följer. Mer information om hur du överför ett certifikat till ett Azure key vault finns [Kom igång med Azure Key Vault](../../key-vault/key-vault-get-started.md).

```powershell
#Give the following values
$VaultName = "mytestvault"
$SecretName = "hpcpfxcert"
$VaultRG = "myresourcegroup"
$location = "westus"
$PfxFile = "c:\Temp\mytest.pfx"
$Password = "yourpfxkeyprotectionpassword"
#Validate the pfx file
try {
    $pfxCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $PfxFile, $Password
}
catch [System.Management.Automation.MethodInvocationException]
{
    throw $_.Exception.InnerException
}
$thumbprint = $pfxCert.Thumbprint
$pfxCert.Dispose()
# Create and encode the JSON object
$pfxContentBytes = Get-Content $PfxFile -Encoding Byte
$pfxContentEncoded = [System.Convert]::ToBase64String($pfxContentBytes)
$jsonObject = @"
{
"data": "$pfxContentEncoded",
"dataType": "pfx",
"password": "$Password"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
#Create an Azure key vault and upload the certificate as a secret
$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
$rg = Get-AzureRmResourceGroup -Name $VaultRG -Location $location -ErrorAction SilentlyContinue
if($null -eq $rg)
{
    $rg = New-AzureRmResourceGroup -Name $VaultRG -Location $location
}
$hpcKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultRG -Location $location -EnabledForDeployment -EnabledForTemplateDeployment
$hpcSecret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secret
"The following Information will be used in the deployment template"
"Vault Name             :   $VaultName"
"Vault Resource Group   :   $VaultRG"
"Certificate URL        :   $($hpcSecret.Id)"
"Certificate Thumbprint :   $thumbprint"

```


## <a name="supported-topologies"></a>Topologier som stöds

Välj något av de [Azure Resource Manager-mallar](https://github.com/MsHpcPack/HPCPack2016) att distribuera HPC Pack 2016-klustret. Följande är med tre exempeltopologier för klustret. Hög tillgänglighet topologier innehåller flera head klusternoder.

1. Kluster med hög tillgänglighet med Active Directory-domän

    ![Hög tillgänglighet till klustret i AD-domänen](./media/hpcpack-2016-cluster/haad.png)


2. Kluster med hög tillgänglighet utan Active Directory-domän

    ![Kluster med hög tillgänglighet utan AD-domänen](./media/hpcpack-2016-cluster/hanoad.png)

3. Kluster med en enda huvudnod

   ![Kluster med enskild huvudnod](./media/hpcpack-2016-cluster/singlehn.png)


## <a name="deploy-a-cluster"></a>Distribuera ett kluster

Välj en mall för att skapa klustret, och klicka på **till Azure**. I den [Azure-portalen](https://portal.azure.com), ange parametrar för mallen enligt beskrivningen i följande steg. Varje mall skapas alla Azure-resurser som krävs för HPC-klusterinfrastruktur. Resurserna innehåller ett virtuellt Azure-nätverk, offentliga IP-adress, belastningsutjämnare (endast för ett kluster med hög tillgänglighet), nätverksgränssnitt, tillgänglighetsuppsättningar, lagringskonton och virtuella datorer.

### <a name="step-1-select-the-subscription-location-and-resource-group"></a>Steg 1: Välj prenumerationen, plats och resursgruppen.

Den **prenumeration** och **plats** måste vara samma som du angav när du har överfört PFX-certifikat (se krav). Vi rekommenderar att du skapar en annan **resursgruppen** för distributionen.

### <a name="step-2-specify-the-parameter-settings"></a>Steg 2: Ange parameterinställningar

Ange eller ändra värden för mallparametrarna. Klicka på ikonen bredvid varje parameter för hjälpinformation. Se även vägledning för [tillgängliga storlekar på VM](sizes.md).

Ange vilka värden som du antecknade i förutsättningarna för följande parametrar: **valvnamnet**, **valvet resursgruppen**, **Certifikatwebbadress**, och  **Tumavtryck för certifikat**.

### <a name="step-3-review-terms-and-create"></a>Steg 3. Läs igenom licensvillkoren och skapa
Granska de allmänna villkoren som är associerad med mallen. Om du godkänner villkoren klickar du på **inköp** att starta distributionen.

Beroende på klustertopologi distributionen kan ta 30 minuter eller längre tid att slutföra.

## <a name="connect-to-the-cluster"></a>Anslut till klustret
1. När klustret HPC Pack distribueras, går du till den [Azure-portalen](https://portal.azure.com). Klicka på **resursgrupper**, och Sök efter den resursgrupp som klustret har distribuerats. Du kan hitta huvudnod virtuella datorer.

    ![Klustrets huvudnoder i portalen](./media/hpcpack-2016-cluster/clusterhns.png)

2. Klicka på en huvudnod (i ett kluster med hög tillgänglighet, klicka på någon av huvudnoderna). I **översikt**, du kan hitta den offentliga IP-adress eller fullständigt DNS-namn för klustret.

    ![Anslutningsinställningar för kluster](./media/hpcpack-2016-cluster/clusterconnect.png)

3. Klicka på **Anslut** att logga in på någon av huvudnoderna med hjälp av fjärrskrivbord med den angivna administratörsanvändarnamn. Om det kluster som du har distribuerat är i Active Directory-domänen, användarnamnet är i formatet \<privateDomainName >\\\<adminUsername > (till exempel hpc.local\hpcadmin).

## <a name="next-steps"></a>Nästa steg
* Skicka jobb till klustret. Se [skicka jobb till HPC Pack ett HPC-kluster i Azure](hpcpack-cluster-submit-jobs.md) och [hantera ett HPC Pack 2016-kluster i Azure med hjälp av Azure Active Directory](hpcpack-cluster-active-directory.md).

