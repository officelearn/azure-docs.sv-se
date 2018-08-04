---
title: Skapa ett Azure Service Fabric-kluster från en mall | Microsoft Docs
description: Den här artikeln beskriver hur du ställer in ett säkert Service Fabric-kluster i Azure med hjälp av Azure Resource Manager, Azure Key Vault och Azure Active Directory (Azure AD) för klientautentisering.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: aljo
ms.openlocfilehash: cad98954c89c37e57d44abf2af54e903a1b4a740
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39504931"
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Skapa ett Service Fabric-kluster med hjälp av Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure-portalen](service-fabric-cluster-creation-via-portal.md)
>
>

Den här stegvisa guiden vägleder dig genom att ställa in ett säkert Azure Service Fabric-kluster i Azure med hjälp av Azure Resource Manager. Vi är medveten om att artikeln är långt. Dock om du redan är bekanta med innehållet, måste du följa stegen noggrant.

Guiden innehåller följande procedurer:

* Nyckelkoncept som du behöver känna till innan du distribuerar Service Fabric-kluster.
* Skapa ett kluster i Azure med hjälp av Service Fabric Resource Manager-modulerna.
* Konfigurera Azure Active Directory (Azure AD) för att autentisera användare som utför hanteringsåtgärder på klustret.
* Redigera en anpassad Azure Resource Manager-mall för ditt kluster och distribuera den.

## <a name="key-concepts-to-be-aware-of"></a>Viktiga begrepp att känna till
I Azure, Service Fabric innehåller principer som du kan använda en x509 certifikat för att säkra ditt kluster och dess slutpunkter. Certifikat används i Service Fabric till att autentisera och kryptera olika delar av ett kluster och de program som körs där. Du kan använda certifikat eller Azure Active Directory-autentiseringsuppgifter för klienten åtkomst/utföra hanteringsåtgärder på klustret, inklusive distribution, uppgradering och borttagning av program, tjänster och den data de innehåller. Användning av Azure Active Directory är mycket rekommenderas eftersom det är det enda sättet att undvika delning av certifikat på klienterna.  Mer information om hur du använder certifikat i Service Fabric finns i [säkerhetsscenarier för Service Fabric-kluster][service-fabric-cluster-security].

Service Fabric använder X.509-certifikat för att skydda ett kluster och säkerhetsfunktioner för programmet. Du använder [Key Vault] [ key-vault-get-started] att hantera certifikat för Service Fabric-kluster i Azure. 


### <a name="cluster-and-server-certificate-required"></a>Klustret och server-certifikat (krävs)
Dessa certifikat (en primär och eventuellt en sekundär) krävs för att skydda ett kluster och förhindra obehörig åtkomst till den. Den tillhandahåller Klustersäkerhet på två sätt:

* **Autentisering för klustret:** autentiserar kommunikation från nod till nod för klustret federation. Endast de noder som kan bevisa sin identitet med det här certifikatet kan ansluta till klustret.
* **Server-autentisering:** autentiserar slutpunkterna för klusterhantering i en Hanteringsklient så att hanteringsklienten vet att det pratar med verkliga klustret och inte en ”man in the middle”. Det här certifikatet ger också en SSL för HTTPS-hanterings-API och för Service Fabric Explorer över HTTPS.

Certifikatet måste uppfylla följande krav för att du har följande syften:

* Certifikatet måste innehålla en privat nyckel. De här certifikaten har vanligtvis tillägg .pfx eller .pem  
* Certifikatet måste skapas för nyckelutbyte som kan exporteras till en Personal Information Exchange (.pfx)-fil.
* Den **certifikatets ämnesnamn måste matcha den domän som du använder för att få åtkomst till Service Fabric-klustret**. Matchningen krävs för att tillhandahålla en SSL för klustrets HTTPS-slutpunkt för hantering och Service Fabric Explorer. Du kan inte hämta ett SSL-certifikat från en certifikatutfärdare (CA) för den *. cloudapp.azure.com domän. Du måste skaffa ett anpassat domännamn för ditt kluster. När du begär ett certifikat från en certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamn du använder för klustret.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>Konfigurera Azure Active Directory för klientautentisering (valfritt men rekommenderas)

Azure AD kan organisationer (kallas även klienter) för att hantera åtkomst till program. Program är indelade i dem med en webbaserad Användargränssnittet för inloggning och personer med en intern klient-upplevelse. I den här artikeln förutsätter vi att du redan har skapat en klient. Om du inte har börjar med att läsa [skaffa en Azure Active Directory-klient][active-directory-howto-tenant].

Service Fabric-kluster erbjuder flera startpunkter för dess hanteringsfunktioner, inklusive den webbaserade [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] och [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Därför kan skapa du två Azure AD-program för att styra åtkomsten till klustret, ett webbprogram och ett internt program.

Mer information om hur du konfigurerar det senare i dokumentet.

### <a name="application-certificates-optional"></a>Certifikat för programmet (valfritt)
Valfritt antal ytterligare certifikat kan installeras på ett kluster av säkerhetsskäl för programmet. Tänk på applikationssäkerhets-scenarier som kräver ett certifikat installeras på noderna, till exempel innan du skapar klustret:

* Kryptering och dekryptering av konfigurationsvärden för programmet.
* Kryptering av data över noder under replikeringen.

Skapa säkra kluster är samma, oavsett om de är Linux eller Windows-kluster. 

### <a name="client-authentication-certificates-optional"></a>Certifikat för klientautentisering (valfritt)
Valfritt antal ytterligare certifikat kan anges för administratör eller användare Klientåtgärder. Klustercertifikatet har administratörsrättigheter för klienten som standard. Dessa ytterligare klientcertifikat bör inte installeras i klustret, den behöver bara anges som tillåts i klusterkonfigurationen, men de måste vara installerad på klientdatorerna så att ansluta till klustret och utföra alla hanteringsåtgärder åtgärder.


## <a name="prerequisites"></a>Förutsättningar 
Skapa säkra kluster är samma, oavsett om de är Linux eller Windows-kluster. Den här guiden beskriver hur du använder Azure PowerShell eller Azure CLI för att skapa nya kluster. Kraven är antingen:

-  [Azure PowerShell 4.1 och senare] [ azure-powershell] eller [Azure CLI 2.0 och senare][azure-CLI].
-  Du hittar information i Service Fabric-moduler här – [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) och [az SF CLI-modulen](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Använda Service Fabric-RM-modulen för att distribuera ett kluster

I det här dokumentet använder vi Service Fabric RM powershell och CLI-modul för att distribuera ett kluster, PowerShell eller CLI-kommando för modulen tillåter flera scenarier. Låt oss gå igenom den dem. Välj det scenario som du tycker bäst uppfyller dina behov. 

- Skapa ett nytt kluster 
    - med hjälp av ett system genereras självsignerat certifikat
    - med hjälp av ett certifikat äger du redan

Du kan använda en standardmall för klustret eller en mall som du redan har

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>Skapa nya kluster – med hjälp av ett system genererade självsignerat certifikat

Använd följande kommando för att skapa kluster om du vill att systemet kan generera ett självsignerat certifikat och använda den för att säkra ditt kluster. Detta kommando ställer in en primär klustercertifikat som används för Klustersäkerhet och för att ställa in administratörsåtkomst för att utföra hanteringsåtgärder som använder certifikatet.

### <a name="login-to-azure"></a>logga in på Azure

```PowerShell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>
```

```CLI
azure login
az account set --subscription $subscriptionId
```
#### <a name="use-the-default-5-node-1-node-type-template-that-ships-in-the-module-to-set-up-the-cluster"></a>Använda 5 nod 1 nod typ standardmallen som levereras i modulen för att konfigurera klustret

Använd följande kommando för att snabbt skapa ett kluster genom att ange minsta nödvändiga parametrarna

Mallen som används är tillgänglig på den [Azure Service Fabric-mallexempel: windows-mall](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) och [Ubuntu-mall](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Kommandona nedan fungerar för att skapa Windows och Linux-kluster du behöver bara ange Operativsystemet i enlighet med detta. PowerShell/CLI-kommandona utdata även certifikatet i den angivna CertificateOutputFolder; dock se till att certifikatmappen som redan har skapats. Kommandot tar även andra parametrar som VM-SKU.

> [!NOTE]
> Den nedan Powershell-kommandot endast fungerar med Azure Resource Managers PowerShell version > 6.1. Kör följande PowerShell-kommando ”Get-Module AzureRM” om du vill kontrollera den aktuella versionen av Azure Resource Managers PowerShell-version. Följ den här länken om du vill uppgradera din Azure Resource Managers PowerShell-version. https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-6.3.0
>
>
```PowerShell
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

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

```CLI
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

#### <a name="use-the-custom-template-that-you-already-have"></a>Använd den anpassade mall som du redan har 

Om du vill skapa en anpassad mall så att den passar dina behov kan vi rekommenderar starkt att du börjar med en av de mallar som är tillgängliga på den [Azure Service Fabric-mallexempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Följ vägledning och förklaringar till [anpassa klustermallen] [ customize-your-cluster-template] nedan.

Om du redan har en anpassad mall och sedan se till att är du dubbelkolla att alla tre certifikatet relaterade parametrar i mallen och parameterfilen namnges enligt följande och värden null på följande sätt.

```Json
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


```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Här är det motsvarande CLI-kommandot för att göra samma sak. Ändra värdena i en declare-instruktioner till lämpliga värden. CLI har stöd för alla andra parametrar som har stöd för ovanstående PowerShell-kommando.

```CLI
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


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>Skapa nytt kluster – med hjälp av certifikatet du köpt från en Certifikatutfärdare eller om du redan har

Använd följande kommando för att skapa kluster om du har ett certifikat som du vill använda för att säkra ditt kluster med.

Om det här är en CA-signerat certifikat som kommer till slut med för andra ändamål samt sedan rekommenderar vi att du anger en specifik resursgrupp specifikt för ditt nyckelvalv. Vi rekommenderar att du placerar i key vault i en egen resursgrupp. Den här åtgärden kan du ta bort de beräkning och lagring resursgrupper, inklusive den resursgrupp som innehåller Service Fabric-klustret, utan att förlora dina nycklar och hemligheter. **Den resursgrupp som innehåller nyckelvalvet _måste vara i samma region_ som klustret som använder den.**


#### <a name="use-the-default-5-node-1-node-type-template-that-ships-in-the-module"></a>Använda 5 nod 1 nod typ standardmallen som levereras i modulen
Mallen som används är tillgänglig på den [Azure-exempel: Windows mallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) och [Ubuntu-mall](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

```CLI
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

#### <a name="use-the-custom-template-that-you-have"></a>Använd den anpassade mall som du har 
Om du vill skapa en anpassad mall så att den passar dina behov kan vi rekommenderar starkt att du börjar med en av de mallar som är tillgängliga på den [Azure Service Fabric-mallexempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Följ vägledning och förklaringar till [anpassa klustermallen] [ customize-your-cluster-template] nedan.

Om du redan har en anpassad mall och sedan se till att är du dubbelkolla att alla tre certifikatet relaterade parametrar i mallen och parameterfilen namnges enligt följande och värden null på följande sätt.

```Json
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


```PowerShell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Här är det motsvarande CLI-kommandot för att göra samma sak. Ändra värdena i en declare-instruktioner till lämpliga värden.

```CLI
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

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-key-vault"></a>Använda en pekare till den hemlighet som du redan har överfört till nyckelvalvet

Att använda ett befintligt nyckelvalv du _måste aktivera för distribution_ att tillåta compute-resursprovidern skaffa certifikat från den och installera den på klusternoder:

```PowerShell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```
Här är det motsvarande CLI-kommandot för att göra samma sak. Ändra värdena i en declare-instruktioner till lämpliga värden.

```CLI
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Konfigurera Azure Active Directory för klientautentisering

Azure AD kan organisationer (kallas även klienter) för att hantera åtkomst till program. Program är indelade i dem med en webbaserad Användargränssnittet för inloggning och personer med en intern klient-upplevelse. I den här artikeln förutsätter vi att du redan har skapat en klient. Om du inte har börjar med att läsa [skaffa en Azure Active Directory-klient][active-directory-howto-tenant].

Service Fabric-kluster erbjuder flera startpunkter för dess hanteringsfunktioner, inklusive den webbaserade [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] och [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Därför kan skapa du två Azure AD-program för att styra åtkomsten till klustret, ett webbprogram och ett internt program.

Vi har skapat en uppsättning Windows PowerShell-skript för att förenkla vissa av stegen som ingår i Konfigurera Azure AD med Service Fabric-kluster.

> [!NOTE]
> Du måste slutföra följande steg innan du skapar klustret. Eftersom skripten förväntar dig klusternamn och slutpunkter, värdena bör planeras och värden inte att du redan har skapat.

1. [Ladda ned skripten] [ sf-aad-ps-script-download] till datorn.
2. Högerklicka på zipfilen, Välj **egenskaper**väljer den **avblockera** och klicka sedan på **tillämpa**.
3. Extrahera zip-filen.
4. Kör `SetupApplications.ps1`, och ange TenantId, klusternamn och WebApplicationReplyUrl som parametrar. Exempel:

```PowerShell
.\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
```

Du hittar ditt TenantId genom att köra PowerShell-kommandot `Get-AzureSubscription`. Kör det här kommandot visar TenantId för varje prenumeration.

Klusternamn används som prefix i Azure AD-program som skapas av skriptet. Det behöver inte matcha det faktiska klusternamnet exakt. Det är endast avsedd att göra det enklare att mappa Azure AD-artefakter till Service Fabric-klustret som de som används med.

WebApplicationReplyUrl är den standardslutpunkt som Azure AD returnerar till användarna när de Slutför inloggningen. Ange den här slutpunkten som Service Fabric Explorer-slutpunkt för ditt kluster, vilket som standard är:

https://&lt;cluster_domain&gt;: 19080/Explorer

Du uppmanas att logga in på ett konto som har administratörsbehörighet för Azure AD-klient. När du har loggat in skapar skriptet webb- och interna program som motsvarar ditt Service Fabric-kluster. Om du tittar på klientens program i den [Azure-portalen][azure-portal], bör du se två nya poster:

   * *Klusternamn*\_kluster
   * *Klusternamn*\_klienten

Skriptet skriver ut den JSON som krävs av Azure Resource Manager-mallen när du skapar klustret i nästa avsnitt, så det är en bra idé att öppna PowerShell-fönstret.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Skapa ett Service Fabric-kluster resource manager-mall
Det här avsnittet är för användare som vill till egna skapar ett Service Fabric-kluster resource manager-mall. När du har en mall kan du fortfarande gå tillbaka och använda PowerShell eller CLI-moduler för att distribuera den. 

Exemplet Resource Manager-mallar är tillgängliga i den [Azure-exempel på GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Dessa mallar kan användas som utgångspunkt för mallen för klustret.

### <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen
Den här guiden används de [säkra kluster med 5 noder] [ service-fabric-secure-cluster-5-node-1-nodetype] exempelmall och mallparametrar. Ladda ned `azuredeploy.json` och `azuredeploy.parameters.json` till din dator och öppna båda filerna i valfri textredigerare.

### <a name="add-certificates"></a>Lägg till certifikat
Du lägger till certifikat till ett kluster resource manager-mall genom att referera till nyckelvalvet som innehåller certifikatnycklarna som. Lägg till dessa key vault-parametrar och värden i en Resource Manager-mall parameterfilen (azuredeploy.parameters.json). 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Lägg till alla certifikat i VM scale set osProfile
Alla certifikat som installeras i klustret måste konfigureras i avsnittet osProfile i scale set-resurs (Microsoft.Compute/virtualMachineScaleSets). Den här åtgärden instruerar resursprovidern att installera certifikatet på de virtuella datorerna. Den här installationen innehåller både klustercertifikatet och alla application security-certifikat som du planerar att använda för dina program:

```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "[parameters('sourceVaultValue')]"
          },
          "vaultCertificates": [
            {
              "certificateStore": "[parameters('clusterCertificateStorevalue')]",
              "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
            },
            {
              "certificateStore": "[parameters('applicationCertificateStorevalue')",
              "certificateUrl": "[parameters('applicationCertificateUrlValue')]"
            },
            ...
          ]
        }
      ]
    }
  }
}
```

#### <a name="configure-the-service-fabric-cluster-certificate"></a>Konfigurera certifikat för Service Fabric-kluster
Certifikatet för autentisering av klustret måste konfigureras i båda Service Fabric klusterresursen (Microsoft.ServiceFabric/clusters) och Service Fabric-tillägget för virtuell datorskalning som anger i VM scale set resursen. Den här ordningen kan konfigureras för användning för autentisering för klustret och serverautentisering för hanteringsslutpunkter Service Fabric-resursprovidern.

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Lägg till certifikatinformation VM-skalningsuppsättningen ange resurs:
```json
{
  "apiVersion": "[variables('vmssApiVersion')]",
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  ...
  "properties": {
    ...
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              ...
              "settings": {
                ...
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
                },
                ...
              }
            }
          }
        ]
      }
    }
  }
}
```

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Lägg till certifikatinformationen till resursen i Service Fabric-kluster:
```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Lägg till Azure AD-konfiguration för att använda Azure AD för klientåtkomst

Du lägger till Azure AD-konfigurationen till ett kluster Resource Manager-mall genom att referera till nyckelvalvet som innehåller certifikatnycklarna som. Lägg till de Azure AD-parametrar och värden i en Resource Manager-mall parameterfilen (azuredeploy.parameters.json).

```json
{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificateCommonNames": {
        "commonNames": [
        {
            "certificateCommonName": "[parameters('certificateCommonName')]",
            "certificateIssuerThumbprint": ""
        }
        ],
        "x509StoreName": "[parameters('certificateStoreValue')]"
    },
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

### <a name="populate-the-parameter-file-with-the-values"></a>Fyll i parameterfilen med värden
Använd slutligen utdatavärden från nyckelvalvet och Azure AD PowerShell-kommandon för att fylla i parameterfilen.

Om du planerar att använda Azure service fabric RM PowerShell-moduler, behöver du inte fylla i certifikatinformationen för klustret. Om du vill systemet för att generera självsignerat signerat certifikat för Klustersäkerhet, bara låta dem vara null. 

> [!NOTE]
> Att hämta och fylla i de här tom parametervärden RM-moduler matchar de parametrar som mycket namnen nedan

```json
"clusterCertificateThumbprint": {
    "value": ""
},
"certificateCommonName": {
    "value": ""
},
"clusterCertificateUrlValue": {
    "value": ""
},
"sourceVaultvalue": {
    "value": ""
},
```

Om du använder certifikat för programmet eller använder ett befintligt kluster som du har överfört till nyckelvalvet måste du hämta den här informationen och fyller den.

RM-moduler har inte möjlighet att skapa Azure AD-konfiguration för dig, så om du planerar att använda Azure AD för klientåtkomst, måste du fylla i den.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        ...
        "clusterCertificateStoreValue": {
            "value": "My"
        },
        "clusterCertificateThumbprint": {
            "value": "<thumbprint>"
        },
        "clusterCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myclustercert/4d087088df974e869f1c0978cb100e47"
        },
        "applicationCertificateStorevalue": {
            "value": "My"
        },
        "applicationCertificateUrlValue": {
            "value": "https://myvault.vault.azure.net:443/secrets/myapplicationcert/2e035058ae274f869c4d0348ca100f08"
        },
        "sourceVaultvalue": {
            "value": "/subscriptions/<guid>/resourceGroups/mycluster-keyvault/providers/Microsoft.KeyVault/vaults/myvault"
        },
        "aadTenantId": {
            "value": "<guid>"
        },
        "aadClusterApplicationId": {
            "value": "<guid>"
        },
        "aadClientApplicationId": {
            "value": "<guid>"
        },
        ...
    }
}
```

### <a name="test-your-template"></a>Testa din mall  
Använd följande PowerShell-kommando för att testa Resource Manager-mallen med en parameterfil:

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Om du stöter på problem och få kryptisk meddelanden, Använd sedan ”-Debug” som ett alternativ.

```PowerShell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Följande diagram illustrerar där dina nyckelvalv och Azure AD-konfiguration passar i Resource Manager-mallen.

![Resource Manager-beroendekarta][cluster-security-arm-dependency-map]


## <a name="encrypting-the-disks-attached-to-your-windows-cluster-nodevirtual-machine-instances"></a>Kryptera diskar kopplade till dina windows kluster nod för virtuella datorinstanser

För att kryptera diskar (OS-enhet och andra hanterade diskar) som är anslutna till din noder, kan vi använda Azure Disk Encryption. Azure Disk Encryption är en ny funktion som hjälper dig att [kryptera din Windows virtuella datordiskar](service-fabric-enable-azure-disk-encryption-windows.md). Azure Disk Encryption utnyttjar branschstandard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funktion i Windows för att kryptera volymer systemvolymen. Lösningen är integrerad med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att styra och hantera diskkrypteringsnycklar och hemligheter i key vault-prenumeration. Lösningen innebär också att alla data på diskar i virtuella datorer krypteras i vila i din Azure-lagring. 

## <a name="encrypting-the-disks-attached-to-your-linux-cluster-nodevirtual-machine-instances"></a>Kryptera diskar som är kopplade till dina Linux-kluster noden/virtuell dator-instanser

För att kryptera diskar (dataenhet och andra hanterade diskar) som är anslutna till din noder, kan vi använda Azure Disk Encryption. Azure Disk Encryption är en ny funktion som hjälper dig att [kryptera dina virtuella Linux-diskar](service-fabric-enable-azure-disk-encryption-linux.md). Azure Disk Encryption utnyttjar branschstandard [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funktion i Linux för att kryptera volymer på diskar. Lösningen är integrerad med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att styra och hantera diskkrypteringsnycklar och hemligheter i key vault-prenumeration. Lösningen innebär också att alla data på diskar i virtuella datorer krypteras i vila i din Azure-lagring. 


## <a name="create-the-cluster-using-azure-resource-template"></a>Skapa klustret med Azure-resursmall 

Du kan nu distribuera kluster med hjälp av stegen som beskrivs tidigare i dokumentet eller om du har värdena i parameterfilen fylls är nu redo att skapa klustret med hjälp av [malldistribution för Azure-resurs] [ resource-group-template-deploy] direkt.

```PowerShell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Om du stöter på problem och få kryptisk meddelanden, Använd sedan ”-Debug” som ett alternativ.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Tilldela användare till roller
När du har skapat de program som ska representera klustret kan tilldela dina användare till roller som stöds av Service Fabric: skrivskyddade och administratör. Du kan tilldela rollerna med hjälp av den [Azure-portalen][azure-portal].

1. Välj din klient i det övre högra hörnet i Azure-portalen.

    ![Välj knappen för klient][select-tenant-button]
2. Välj **Azure Active Directory** på den vänstra fliken och väljer ”program”.
3. Välj ”alla program”, och leta upp och välj webbprogram som har ett namn som `myTestCluster_Cluster`.
4. Klicka på den **användare och grupper** fliken.

    ![Fliken användare och grupper][users-and-groups-tab]
5. Klicka på den **Lägg till användare** knapp på den nya sidan, Välj en användare och rollen för att tilldela och klicka sedan på den **Välj** längst ned på sidan.

    ![Tilldela användare till roller sida][assign-users-to-roles-page]
6. Klicka på den **tilldela** längst ned på sidan.

    ![Lägg till tilldelning bekräftelse][assign-users-to-roles-confirm]

> [!NOTE]
> Mer information om roller i Service Fabric finns i [rollbaserad åtkomstkontroll för Service Fabric-klienter](service-fabric-cluster-security-roles.md).
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Hjälp att konfigurera Azure Active Directory med felsökning
Konfigurera Azure AD och använda det kan vara en utmaning, så här följer några tips på vad du kan göra för att felsöka problemet.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer uppmanar dig att välja ett certifikat
#### <a name="problem"></a>Problem
När du har loggat in har till Azure AD i Service Fabric Explorer returneras från webbläsaren till startsidan, men ett meddelande som uppmanar dig att välja ett certifikat.

![Dialogrutan för SFX-certifikat][sfx-select-certificate-dialog]

#### <a name="reason"></a>Orsak
Användaren är inte tilldelad en roll i Azure AD-programmet för klustret. Azure AD-autentisering går därför inte i Service Fabric-kluster. Service Fabric Explorer faller tillbaka till autentisering med datorcertifikat.

#### <a name="solution"></a>Lösning
Följ anvisningarna för att konfigurera Azure AD och tilldela användarroller. Dessutom rekommenderar vi att du aktiverar ”Användartilldelning krävs för att få åtkomst till appen”, som `SetupApplications.ps1` har.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Anslutningen med PowerShell misslyckas med ett fel: ”de angivna autentiseringsuppgifterna är ogiltiga”
#### <a name="problem"></a>Problem
När du använder PowerShell för att ansluta till klustret med hjälp av läget för ”AzureActiveDirectory” säkerhet när du loggar in har till Azure AD, om anslutningen misslyckas med ett fel: ”de angivna autentiseringsuppgifterna är ogiltiga”.

#### <a name="solution"></a>Lösning
Den här lösningen är samma som det föregående.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer returnerar ett fel när du loggar in: ”AADSTS50011”
#### <a name="problem"></a>Problem
När du försöker logga in på Azure AD i Service Fabric Explorer sidan returnerar ett fel ”: AADSTS50011: svarsadressen &lt;url&gt; matchar inte svars-adresser som har konfigurerats för programmet: &lt;guid&gt;”.

![SFX svarsadressen matchar inte][sfx-reply-address-not-match]

#### <a name="reason"></a>Orsak
Klustret (webb)-program som motsvarar Service Fabric Explorer försöker autentisera mot Azure AD och som en del av begäran ger den returnera omdirigerings-URL. Men URL: en har inte listats i Azure AD-programmet **SVARS-URL** lista.

#### <a name="solution"></a>Lösning
Välj ”appregistreringar” i AAD-sidan, Välj ditt program i klustret och välj sedan den **Svarswebbadresser** knappen. Lägg till URL: en för Service Fabric Explorer i listan på sidan ”svars-URL: er eller ersätta ett av objekten i listan. När du är klar kan du spara ändringarna.

![Web application svars-url][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Ansluta till klustret med hjälp av Azure AD-autentisering via PowerShell
Använd följande PowerShell-exemplet för att ansluta Service Fabric-klustret:

```PowerShell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Läs om cmdleten Connect-ServiceFabricCluster i [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Kan jag återanvända samma Azure AD-klienten i flera kluster?
Ja. Men kom ihåg att lägga till URL: en för Service Fabric Explorer i ditt program i klustret (webb). I annat fall fungerar Service Fabric Explorer inte.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Varför jag fortfarande behöver ett certifikat medan Azure AD är aktiverat?
FabricClient och FabricGateway utför du en ömsesidig autentisering. Azure AD-integrering ger en klientens identitet till servern under Azure AD-autentisering och certifikatet används för att verifiera serveridentitet. Läs mer om Service Fabric-certifikat, [X.509-certifikat och Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Nästa steg
Nu har du ett säkert kluster med Azure Active Directory med hantering av autentisering. Nästa [ansluta till klustret](service-fabric-connect-to-secure-cluster.md) och lär dig hur du [hantera programhemligheter](service-fabric-application-secret-management.md).


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#create-a-service-fabric-cluster-resource-manager-template

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[select-tenant-button]: ./media/service-fabric-cluster-creation-via-arm/select-tenant-button.png
[users-and-groups-tab]: ./media/service-fabric-cluster-creation-via-arm/users-and-groups-tab.png
[assign-users-to-roles-page]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-page.png
[assign-users-to-roles-confirm]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-confirm.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

