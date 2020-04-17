---
title: Skapa ett Azure Service Fabric-kluster
description: Lär dig hur du konfigurerar ett säkert Service Fabric-kluster i Azure med Hjälp av Azure Resource Manager.  Du kan skapa ett kluster med hjälp av en standardmall eller med hjälp av din egen klustermall.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: 24bc80a7ab3ce61b79466cdd943c60efd367ce01
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458308"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Skapa ett Service Fabric-kluster med Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Ett [Azure Service Fabric-kluster](service-fabric-deploy-anywhere.md) är en nätverksansluten uppsättning virtuella datorer som dina mikrotjänster distribueras och hanteras till.  Ett Service Fabric-kluster som körs i Azure är en Azure-resurs och distribueras med Hjälp av Azure Resource Manager. I den här artikeln beskrivs hur du distribuerar ett säkert Service Fabric-kluster i Azure med hjälp av Resource Manager. Du kan använda en standardklustermall eller en anpassad mall.  Om du inte redan har en anpassad mall kan du [läsa om hur](service-fabric-cluster-creation-create-template.md)du skapar en .

Den typ av säkerhet som valts för att skydda klustret (dvs. Windows-identitet, X509 etc.) måste anges för det första skapandet av klustret och kan inte ändras därefter. Läs [säkerhetsscenarier för Service Fabric-kluster][service-fabric-cluster-security]innan du konfigurerar ett kluster . I Azure använder Service Fabric x509-certifikat för att skydda klustret och dess slutpunkter, autentisera klienter och kryptera data. Azure Active Directory rekommenderas också för att skydda åtkomst till hanteringsslutpunkter. Mer information finns i [Konfigurera Azure AD för att autentisera klienter](service-fabric-cluster-creation-setup-aad.md).

Om du skapar ett produktionskluster för att köra produktionsarbetsbelastningar rekommenderar vi att du först läser igenom [checklistan för produktionsberedskap](service-fabric-production-readiness-checklist.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav 
I den här artikeln använder du Service Fabric RM powershell- eller Azure CLI-modulerna för att distribuera ett kluster:

* [Azure PowerShell 4.1 och senare][azure-powershell]
* [Azure CLI version 2.0 och senare][azure-CLI]

Du hittar referensdokumentationen för Service Fabric-modulerna här:
* [Az.ServiceFabric](https://docs.microsoft.com/powershell/module/az.servicefabric)
* [az SF CLI modul](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Logga in på Azure

Innan du kör något av kommandona i den här artikeln loggar du först in på Azure.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Skapa ett nytt kluster med ett självsignerat systemcertifikat som genereras

Använd följande kommandon för att skapa ett kluster som skyddas med ett systemgenererat självsignerat certifikat. Det här kommandot ställer in ett primärt klustercertifikat som används för klustersäkerhet och för att konfigurera administratörsåtkomst för att utföra hanteringsåtgärder med certifikatet.  Självsignerade certifikat är användbara för att skydda testkluster.  Produktionskluster bör skyddas med ett certifikat från en certifikatutfärdare.

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>Använda standardklustermallen som levereras i modulen

Använd följande kommando för att skapa ett kluster snabbt, genom att ange minimala parametrar, med hjälp av standardmallen.

Mallen som används är tillgänglig i mallexemplen i [Azure Service Fabric: windowsmall](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) och [Ubuntu-mall](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Följande kommando kan skapa antingen Windows eller Linux-kluster, måste du ange operativsystemet därefter. PowerShell/CLI-kommandona matar också ut certifikatet i den angivna *CertificateOutputFolder.* Kontrollera dock att certifikatmappen redan har skapats. Kommandot tar in andra parametrar som VM SKU också.

> [!NOTE]
> Följande PowerShell-kommando fungerar bara med `Az` Azure PowerShell-modulen. Om du vill kontrollera den aktuella versionen av Azure Resource Manager PowerShell-versionen kör du följande PowerShell-kommando "Get-Module Az". Följ [den här länken](/powershell/azure/install-Az-ps) för att uppgradera din Azure Resource Manager PowerShell-version. 
>
>

Distribuera klustret med PowerShell:

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

Distribuera klustret med Azure CLI:

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

### <a name="use-your-own-custom-template"></a>Använda din egen anpassade mall

Om du behöver skapa en anpassad mall som passar dina behov rekommenderar vi starkt att du börjar med en av de mallar som finns tillgängliga i [mallexemplen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)i Azure Service Fabric . Lär dig hur du [anpassar klustermallen][customize-your-cluster-template].

Om du redan har en anpassad mall dubbelkollar du att alla tre certifikatrelaterade parametrar i mallen och parameterfilen namnges enligt följande och värden är null enligt följande:

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

Distribuera klustret med PowerShell:

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

Distribuera klustret med Azure CLI:

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

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Skapa ett nytt kluster med ditt eget X.509-certifikat

Använd följande kommando för att skapa kluster, om du har ett certifikat som du vill använda för att skydda klustret med.

Om detta är ett certifikatutfärdarsignerat certifikat som du även kommer att använda för andra ändamål, rekommenderar vi att du tillhandahåller en separat resursgrupp specifikt för nyckelvalvet. Vi rekommenderar att du placerar nyckelvalvet i en egen resursgrupp. Med den här åtgärden kan du ta bort beräknings- och lagringsresursgrupperna, inklusive resursgruppen som innehåller ditt Service Fabric-kluster, utan att förlora dina nycklar och hemligheter. **Resursgruppen som innehåller nyckelvalvet *måste finnas i samma region* som klustret som använder det.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Använd standardmallen fem nod, en nodtypmall som levereras i modulen
Mallen som används är tillgänglig i [Azure-exempel: Windows-mallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) och [Ubuntu-mallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Distribuera klustret med PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Distribuera klustret med Azure CLI:

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

### <a name="use-your-own-custom-cluster-template"></a>Använda din egen anpassade klustermall
Om du behöver skapa en anpassad mall som passar dina behov rekommenderar vi starkt att du börjar med en av de mallar som finns tillgängliga i [mallexemplen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)i Azure Service Fabric . Lär dig hur du [anpassar klustermallen][customize-your-cluster-template].

Om du redan har en anpassad mall, se till att dubbelkolla att alla tre certifikat relaterade parametrar i mallen och parameterfilen heter enligt följande och värden är null enligt följande.

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

Distribuera klustret med PowerShell:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Distribuera klustret med Azure CLI:

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

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Använda en pekare till en hemlighet som laddats upp i ett nyckelvalv

Om du vill använda ett befintligt nyckelvalv måste nyckelvalvet vara [aktiverat för distribution så](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI) att beräkningsresursprovidern kan hämta certifikat från det och installera det på klusternoder.

Distribuera klustret med PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Distribuera klustret med Azure CLI:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier $secretID  \
    --template-file $templateFilePath --parameter-file $parameterFilePath 
```

## <a name="next-steps"></a>Nästa steg
Nu har du ett säkert kluster som körs i Azure. Anslut [sedan till klustret](service-fabric-connect-to-secure-cluster.md) och lär dig hur du [hanterar programhemligheter](service-fabric-application-secret-management.md).

Den JSON-syntax och de egenskaper som ska användas i en mall finns i [Microsoft.ServiceFabric/clusters template reference](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
