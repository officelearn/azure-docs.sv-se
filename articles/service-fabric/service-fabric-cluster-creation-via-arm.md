---
title: Skapa ett Azure Service Fabric-kluster | Microsoft Docs
description: Lär dig hur du ställer in ett säkert Service Fabric-kluster i Azure med Azure Resource Manager.  Du kan skapa ett kluster med hjälp av en standardmall eller en egen mall för klustret.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: 52623183139be2b8ac6b12d3adca64e72de932d3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050330"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Skapa ett Service Fabric-kluster med Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

En [Azure Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella datorer som dina mikrotjänster distribueras och hanteras.  Service Fabric-kluster som körs i Azure är en Azure-resurs och distribueras med hjälp av Azure Resource Manager. Den här artikeln beskriver hur du distribuerar ett säkert Service Fabric-kluster i Azure med hjälp av Resource Manager. Du kan använda en standardmall för klustret eller en anpassad mall.  Om du inte redan har en anpassad mall, kan du [Lär dig hur du skapar ett](service-fabric-cluster-creation-create-template.md).

Klustersäkerhet konfigureras när klustret är första installationen och kan inte ändras senare. Innan du konfigurerar ett kluster, läsa [säkerhetsscenarier för Service Fabric-kluster][service-fabric-cluster-security]. I Azure, Service Fabric använder x509 certifikat till säkra ditt kluster och dess slutpunkter, autentisera klienter och kryptera data. Azure Active Directory rekommenderas för säker åtkomst till av hanteringsslutpunkter. Azure AD-klienter och användare måste skapas innan du skapar klustret.  Mer information finns i [ställa in Azure AD för att autentisera klienter](service-fabric-cluster-creation-setup-aad.md).

Om du skapar ett produktionskluster för att köra arbetsbelastningar i produktion, rekommenderar vi att du första läsa igenom den [produktion beredskap checklista](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter 
I den här artikeln använder du Service Fabric RM powershell eller Azure CLI-moduler för att distribuera ett kluster:

* [Azure PowerShell 4.1 och senare][azure-powershell]
* [Azure CLI version 2.0 och senare][azure-CLI]

Du kan hitta referensdokumentation för Service Fabric-moduler här:
* [Az.ServiceFabric](https://docs.microsoft.com/powershell/module/az.servicefabric)
* [AZ SF CLI-modulen](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Logga in på Azure

Innan du kör något av kommandona i den här artikeln måste först logga in på Azure.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Skapa ett nytt kluster med hjälp av ett självsignerat certifikat för systemgenererade

Använd följande kommandon för att skapa ett kluster som skyddas med ett självsignerat certifikat för genereras av systemet. Detta kommando ställer in en primär klustercertifikat som används för Klustersäkerhet och för att ställa in administratörsåtkomst för att utföra hanteringsåtgärder som använder certifikatet.  Självsignerade certifikat är användbara för att skydda testkluster.  Produktionskluster bör skyddas med ett certifikat från en certifikatutfärdare (CA).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Använda kluster standardmall som levereras i modulen

Använd följande kommando för att skapa ett kluster snabbt, genom att ange minsta nödvändiga parametrarna, med hjälp av standardmallen.

Mallen som används är tillgänglig på den [Azure Service Fabric-mallexempel: windows-mall](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) och [Ubuntu-mall](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Följande kommando kan skapa antingen Windows eller Linux-kluster, måste du ange Operativsystemet på lämpligt sätt. PowerShell/CLI-kommandona utdata även certifikatet i den angivna *CertificateOutputFolder*, men se till att certifikatmappen som redan har skapats. Kommandot tar även andra parametrar, till exempel VM-SKU.

> [!NOTE]
> Följande PowerShell-kommando fungerar bara med Azure PowerShell `Az` modulen. Kör följande PowerShell-kommando ”Get-Module Az” om du vill kontrollera den aktuella versionen av Azure Resource Managers PowerShell-version. Följ [den här länken](/powershell/azure/install-Az-ps) att uppgradera din Azure Resource Managers PowerShell-version. 
>
>

Distribuera kluster med hjälp av PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Distribuera kluster med Azure CLI:

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>Använda en egen anpassad mall

Om du vill skapa en anpassad mall så att den passar dina behov kan vi rekommenderar starkt att du börjar med en av de mallar som är tillgängliga på den [Azure Service Fabric-mallexempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Lär dig hur du [anpassa klustermallen][customize-your-cluster-template].

Om du redan har en anpassad mall kan du kontrollera att alla tre certifikatet relaterade parametrar i mallen och parameterfilen namnges enligt följande och värdena är null på följande sätt:

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Distribuera kluster med hjälp av PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Distribuera kluster med Azure CLI:

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Skapa ett nytt kluster med hjälp av dina egna X.509-certifikat

Använd följande kommando för att skapa kluster om du har ett certifikat som du vill använda för att säkra ditt kluster med.

Om det här är en CA-signerat certifikat som kommer till slut med för andra ändamål samt sedan rekommenderar vi att du anger en specifik resursgrupp specifikt för ditt nyckelvalv. Vi rekommenderar att du placerar i key vault i en egen resursgrupp. Den här åtgärden kan du ta bort de beräkning och lagring resursgrupper, inklusive den resursgrupp som innehåller Service Fabric-klustret, utan att förlora dina nycklar och hemligheter. **Den resursgrupp som innehåller nyckelvalvet *måste vara i samma region* som klustret som använder den.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Använd standard fem noder, en nod typen mall som levereras i modulen
Mallen som används är tillgänglig på den [Azure-exempel: Windows-mallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) och [Ubuntu-mall](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Distribuera kluster med hjälp av PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Distribuera kluster med Azure CLI:

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>Använd dina egna anpassade kluster-mall
Om du vill skapa en anpassad mall så att den passar dina behov kan vi rekommenderar starkt att du börjar med en av de mallar som är tillgängliga på den [Azure Service Fabric-mallexempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Lär dig hur du [anpassa klustermallen][customize-your-cluster-template].

Om du redan har en anpassad mall och sedan se till att är du dubbelkolla att alla tre certifikatet relaterade parametrar i mallen och parameterfilen namnges enligt följande och värden null på följande sätt.

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Distribuera kluster med hjälp av PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Distribuera kluster med Azure CLI:

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Använda en pekare till en hemlighet som har överförts till ett nyckelvalv

Om du vill använda ett befintligt nyckelvalv nyckelvalvet måste vara [aktiverat för distribution av](../key-vault/key-vault-manage-with-cli2.md#bkmk_KVperCLI) att tillåta compute-resursprovidern skaffa certifikat från den och installera den på klusternoderna.

Distribuera kluster med hjälp av PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Distribuera kluster med Azure CLI:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

## <a name="next-steps"></a>Nästa steg
Nu har du ett säkert kluster som körs i Azure. Nästa [ansluta till klustret](service-fabric-connect-to-secure-cluster.md) och lär dig hur du [hantera programhemligheter](service-fabric-application-secret-management.md).

JSON-syntax och egenskaper för att använda en mall finns i [Microsoft.ServiceFabric/clusters mallreferensen](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
