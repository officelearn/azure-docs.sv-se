---
title: "Skapa ett Azure Service Fabric-kluster från en mall | Microsoft Docs"
description: "Den här artikeln beskriver hur du ställer in en säker Service Fabric-kluster i Azure med Azure Resource Manager, Azure Key Vault och Azure Active Directory (Azure AD) för klientautentisering."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: chackdan
ms.openlocfilehash: 19ad079c58728479a7f39ce7520bd8eaebdff1f5
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Skapa ett Service Fabric-kluster med hjälp av Azure Resource Manager 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Den här stegvisa guiden vägleder dig genom att skapa en säker Azure Service Fabric-kluster i Azure med hjälp av Azure Resource Manager. Vi bekräftar att artikeln är långt. Dock om du redan är väl bekanta dig med innehållet, måste du följa varje steg noggrant.

Guiden innehåller följande procedurer:

* Nyckelkoncept som du behöver känna av innan du distribuerar ett service fabric-kluster.
* Skapar ett kluster i Azure med hjälp av service fabric Resource Manager moduler.
* Konfigurera Azure Active Directory (Azure AD) för att autentisera användare utföra hanteringsåtgärder på klustret.
* Redigera en anpassad Azure Resource Manager-mall för klustret och distribuera den.

## <a name="key-concepts-to-be-aware-of"></a>Viktiga begrepp att vara medveten om
I Azure Service fabric kräver som du kan använda x509 certifikat för säker klustret och dess slutpunkter. Certifikat används i Service Fabric till att autentisera och kryptera olika delar av ett kluster och de program som körs där. Du kan använda certifikat eller Azure Active Directory-autentiseringsuppgifter för klienten åtkomst/utföra hanteringsåtgärder på klustret, inklusive distribution, uppgradera och ta bort program, tjänster och de data som de innehåller. Användning av Azure Active Directory är hög rekommenderar eftersom det är det enda sättet att förhindra delning av certifikat på klienterna.  Mer information om hur du använder certifikat i Service Fabric finns [säkerhetsscenarier för Service Fabric-klustret][service-fabric-cluster-security].

Service Fabric använder X.509-certifikat för att skydda ett kluster och säkerhetsfunktioner för programmet. Du använder [Nyckelvalvet] [ key-vault-get-started] att hantera certifikat för Service Fabric-kluster i Azure. 


### <a name="cluster-and-server-certificate-required"></a>Kluster- och certifikat (krävs)
Dessa certifikat (en primär och eventuellt en sekundär) krävs för att skydda ett kluster och förhindra obehörig åtkomst till den. Det ger säkerhet i klustret på två sätt:

* **Autentisering för klustret:** autentiserar nod till nod kommunikation för klustret. Endast noder som kan bevisa sin identitet med det här certifikatet kan ansluta till klustret.
* **Serverautentisering:** verifierar att klustret management slutpunkter management-klienten så att klienten management vet pratar till verkliga kluster och inte en 'man i mitten'. Det här certifikatet ger också en SSL för HTTPS-hanterings-API och för Service Fabric Explorer via HTTPS.

Om du vill hantera dessa ändamål måste certifikatet uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel. De här certifikaten har vanligtvis tillägg PFX- eller .pem  
* Certifikatet måste skapas för nyckelutbyte, som kan exporteras till en Personal Information Exchange (.pfx)-fil.
* Den **certifikatets ämnesnamn måste matcha den domän som du använder för att få åtkomst till Service Fabric-klustret**. Den här matchar krävs att tillhandahålla en SSL för klustrets HTTPS-slutpunkt för hantering och Service Fabric Explorer. Du kan skaffa ett SSL-certifikat från en certifikatutfärdare (CA) för den *. cloudapp.azure.com domän. Du måste skaffa ett anpassat domännamn för klustret. När du begär ett certifikat från en Certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som du använder för klustret.

### <a name="set-up-azure-active-directory-for-client-authentication-optional-but-recommended"></a>Konfigurera Azure Active Directory för klientautentisering (valfritt men rekommenderas)

Azure AD kan organisationer (kallas även klienter) för att hantera användarnas åtkomst till program. Program är indelade i dem med en webbaserad inloggning användargränssnitt och de med inbyggda klientupplevelsen. I den här artikeln förutsätter vi att du redan har skapat en klient. Om du inte har börja med att läsa [skaffa en Azure Active Directory-klient][active-directory-howto-tenant].

Ett Service Fabric-kluster erbjuder flera startpunkter till dess hanteringsfunktioner, inklusive den webbaserade [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] och [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Därför kan skapa du två Azure AD-program för att styra åtkomsten till klustret, ett webbprogram och en programspecifika.

Mer information om hur du ställer in den senare i dokumentet.

### <a name="application-certificates-optional"></a>Certifikat för programmet (valfritt)
Valfritt antal ytterligare certifikat kan installeras på ett kluster av säkerhetsskäl för programmet. Tänk på applikationssäkerhets-scenarier som kräver ett certifikat installeras på noderna, som innan du skapar klustret:

* Kryptering och dekryptering av konfigurationsvärden för programmet.
* Kryptering av data mellan noderna under replikeringen.

Konceptet för att skapa skyddade kluster är densamma, oavsett om de är Linux eller Windows-kluster. 

### <a name="client-authentication-certificates-optional"></a>Certifikat för klientautentisering (valfritt)
Valfritt antal ytterligare certifikat kan anges för administratören eller användaren Klientåtgärder. Klustret certifikatet har administratörsrättigheter för klienten som standard. Dessa ytterligare klientcertifikat bör inte installeras i klustret, det behöver anges som tillåts i klusterkonfigurationen, men de måste installeras på klientdatorer att ansluta till klustret och utföra alla hantering åtgärder.


## <a name="prerequisites"></a>Krav 
Konceptet för att skapa skyddade kluster är densamma, oavsett om de är Linux eller Windows-kluster. Den här guiden omfattar användning av azure powershell eller azure CLI för att skapa nya kluster. Nödvändiga komponenter är antingen 

-  [Azure PowerShell 4.1 och senare] [ azure-powershell] eller [Azure CLI 2.0 och senare][azure-CLI].
-  Du hittar information om tjänsten fabic moduler här - [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric) och [az SA CLI-modul](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)


## <a name="use-service-fabric-rm-module-to-deploy-a-cluster"></a>Använda service fabric RM-modulen för att distribuera ett kluster

I det här dokumentet använder vi service fabric RM powershell och CLI modulen att distribuera ett kluster, powershell eller kommandot CLI modulen gör för scenarier med flera. Låt oss gå igenom den dem. Välj det scenario som du tycker bäst uppfyller dina behov. 

- Skapa ett nytt kluster - med hjälp av ett system genererade självsignerat certifikat
    - Använda en standardmall för kluster
    - Använd en mall som du redan har
- Skapa ett nytt kluster - använder ett certifikat som du redan äger
    - Använda en standardmall för kluster
    - Använd en mall som du redan har

### <a name="create-new-cluster----using-a-system-generated-self-signed-certificate"></a>Skapa nytt kluster - med hjälp av ett system genererade självsignerat certifikat

Använd följande kommando för att skapa klustret, om du vill att systemet ska generera ett självsignerat certifikat och använda den för att skydda klustret. Detta kommando ställer in ett certifikat för primär kluster som används för klustret säkerhet och ställa in administratörsåtkomst för att utföra hanteringsåtgärder som använder certifikatet.

### <a name="login-in-to-azure"></a>logga in i Azure.

```Powershell

Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <guid>

```

```CLI

azure login
az account set --subscription $subscriptionId

```
#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module-to-set-up-the-cluster"></a>Använda 5 nod 1 nodetype standardmallen som levereras i modulen för att skapa klustret

Använd följande kommando för att snabbt skapa ett kluster genom att ange minimal parametrar

Den mall som används är tillgänglig på den [azure service fabric mallen prover: windows-mall](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) och [Ubuntu mall](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Kommandona nedan fungerar för att skapa Windows- och Linux-kluster, du behöver bara ange OS därefter. Powershell / CLI-kommandona också matar ut certifikat certifikatet i anges i theCertificateOutputFolder. Kommandot tar in samt andra parametrar som VM SKU.

```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

```

```CLI

declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!1"
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

Om du behöver skapa en anpassad mall så att de passar dina behov, rekommenderas att du börjar med en av de mallar som är tillgängliga på den [azure service fabric mallen exempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Följ vägledning och förklaringar till [anpassa mallen för klustret] [ customize-your-cluster-template] nedan.

Om du redan har en anpassad mall och sedan kontrollera att är dubbelkolla som alla tre certifikatet relaterade parametrar i mallen och parameterfilen är namngivna enligt följande och värden null som följer.

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


```Powershell


$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```

Här är motsvarande CLI-kommando för att göra detsamma. Ändra värdena i declare-satser till lämpliga värden. CLI har stöd för alla andra parametrar som har stöd för ovanstående powershell-kommando.

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


### <a name="create-new-cluster---using-the-certificate-you-bought-from-a-ca-or-you-already-have"></a>Skapa nytt kluster - med certifikat som du har köpt från en Certifikatutfärdare eller om du redan har.

Använda följande kommando för att skapa kluster, om du har ett certifikat som du vill använda för att skydda ditt kluster med.

Om det här är en CA-signerat certifikat som du kommer att få med för andra ändamål samt sedan rekommenderas det att du anger en distinkta resursgrupp specifikt för nyckelvalvet. Vi rekommenderar att du placera nyckelvalvet i sin egen resursgruppen. Den här åtgärden kan du ta bort beräkning och lagring resursgrupper, inklusive resursgruppen som innehåller Service Fabric-klustret utan att förlora dina nycklar och hemligheter. **Resursgruppen som innehåller nyckelvalvet _måste vara i samma region_ som klustret som använder den.**


#### <a name="use-the-default-5-node-1-nodetype-template-that-ships-in-the-module"></a>Använda 5 nod 1 nodetype standardmallen som levereras i modulen
Den mall som används är tillgänglig på den [azure-exempel: windows-mall](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) och [Ubuntu mall](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

```Powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!1" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 

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
Om du behöver skapa en anpassad mall så att de passar dina behov, rekommenderas att du börjar med en av de mallar som är tillgängliga på den [azure service fabric mallen exempel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Följ vägledning och förklaringar till [anpassa mallen för klustret] [ customize-your-cluster-template] nedan.

Om du redan har en anpassad mall och sedan kontrollera att är dubbelkolla som alla tre certifikatet relaterade parametrar i mallen och parameterfilen är namngivna enligt följande och värden null som följer.

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


```Powershell

$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResouceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword #certPassword

```

Här är motsvarande CLI-kommando för att göra detsamma. Ändra värdena i declare-satser till lämpliga värden.

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

#### <a name="use-a-pointer-to-the-secret-you-already-have-uploaded-into-the-keyvault"></a>Använd en pekare till den hemlighet som redan har överförts till keyvault

Att använda en befintlig nyckelvalv du _måste aktivera den för distribution_ att compute-resursprovidern så att hämta certifikat från det och installera den på klusternoder:

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment


$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroup -SecretIdentifier $secretID -TemplateFile $templateFile -ParameterFile $templateParmfile 

```
Här är motsvarande CLI-kommando för att göra detsamma. Ändra värdena i declare-satser till lämpliga värden.

```cli

declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifieraz $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 

```

<a id="add-AAD-for-client"></a>

## <a name="set-up-azure-active-directory-for-client-authentication"></a>Konfigurera Azure Active Directory för klientautentisering

Azure AD kan organisationer (kallas även klienter) för att hantera användarnas åtkomst till program. Program är indelade i dem med en webbaserad inloggning användargränssnitt och de med inbyggda klientupplevelsen. I den här artikeln förutsätter vi att du redan har skapat en klient. Om du inte har börja med att läsa [skaffa en Azure Active Directory-klient][active-directory-howto-tenant].

Ett Service Fabric-kluster erbjuder flera startpunkter till dess hanteringsfunktioner, inklusive den webbaserade [Service Fabric Explorer] [ service-fabric-visualizing-your-cluster] och [Visual Studio] [ service-fabric-manage-application-in-visual-studio]. Därför kan skapa du två Azure AD-program för att styra åtkomsten till klustret, ett webbprogram och en programspecifika.

Vi har skapat en uppsättning Windows PowerShell-skript för att förenkla vissa av stegen som ingår i att konfigurera Azure AD med ett Service Fabric-kluster.

> [!NOTE]
> Du måste slutföra följande steg innan du skapar klustret. Eftersom skripten förväntas klusternamn och slutpunkter kan värdena bör planeras och värden inte som du redan har skapat.

1. [Hämta skripten] [ sf-aad-ps-script-download] till datorn.
2. Högerklicka på zip-filen, Välj **egenskaper**, Välj den **avblockera** kryssrutan och klicka sedan på **tillämpa**.
3. Extrahera zip-filen.
4. Kör `SetupApplications.ps1`, och anger TenantId, klusternamn och WebApplicationReplyUrl som parametrar. Exempel:

```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'

```

Du kan hitta din TenantId genom att köra PowerShell-kommando `Get-AzureSubscription`. Kör det här kommandot visar TenantId för varje prenumeration.

Klusternamn används som prefix i Azure AD-program som skapats av skriptet. Det behöver inte matcha exakt faktiska klustrets namn. Det är endast avsedd att göra det enklare att mappa Service Fabric-klustret som de som används med Azure AD-artefakter.

WebApplicationReplyUrl är standardslutpunkten som Azure AD tillbaka till användarna när de har loggat in. Ange den här slutpunkten som Service Fabric Explorer-slutpunkt för klustret, vilket som standard är:

https://&lt;cluster_domain&gt;: 19080/Explorer

Du uppmanas att logga in på ett konto som har administratörsbehörighet för Azure AD-klient. När du loggar in skapar skriptet webb- och interna program för att representera Service Fabric-klustret. Om du tittar på klientens program i den [Azure-portalen][azure-portal], bör du se två nya poster:

   * *Klusternamn*\_kluster
   * *Klusternamn*\_klienten

Skriptet skriver ut JSON som krävs av Azure Resource Manager-mallen när du skapar klustret i nästa avsnitt, så det är en bra idé att öppna PowerShell-fönstret.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

<a id="customize-arm-template" ></a>

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Skapa en mall för Service Fabric-kluster Resource Manager
Det här avsnittet är för användare som vill anpassad skapar Service Fabric-kluster Resource Manager-mall. När du har en mall kan du fortfarande gå tillbaka och använda powershell eller CLI-moduler för att distribuera den. 

Exempel Resource Manager-mallar är tillgängliga i den [Azure exemplen på GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates). Dessa mallar kan användas som en startpunkt för mallen för klustret.

### <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen
Den här guiden använder den [säker kluster med 5] [ service-fabric-secure-cluster-5-node-1-nodetype] exempel mallen och mallparametrar. Hämta `azuredeploy.json` och `azuredeploy.parameters.json` till din dator och öppna filer i valfri textredigerare.

### <a name="add-certificates"></a>Lägg till certifikat
Du lägga till certifikat i ett kluster Resource Manager-mall genom att referera till nyckelvalvet som innehåller certifikatnycklarna. Lägg till dessa nyckelvalvet parametrar och värden i en Resource Manager parametrar mallfil (azuredeploy.parameters.json). 

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Lägg till alla certifikat i virtual machine scale set osProfile
Alla certifikat som installeras i klustret måste konfigureras i avsnittet osProfile i scale set-resurs (Microsoft.Compute/virtualMachineScaleSets). Den här åtgärden instruerar resursprovidern att installera certifikatet på virtuella datorer. Den här installationen innehåller både certifikatet kluster och alla program security-certifikat som du planerar att använda för dina program:

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
Certifikatet för autentisering av klustret måste konfigureras i båda Service Fabric klusterresursen (Microsoft.ServiceFabric/clusters) och Service Fabric-tillägget för virtuell dator skala anger i den virtuella dator skala set resursen. Den här ordningen kan Service Fabric-resursprovidern så att konfigurera den som ska användas för autentisering för klustret och serverautentisering för av hanteringsslutpunkter.

##### <a name="add-the-certificate-information-the-virtual-machine-scale-set-resource"></a>Lägga till certifikatinformationen virtuella datorns skaluppsättning resurs:
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
                  "thumbprint": "[parameters('clusterCertificateThumbprint')]",
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

##### <a name="add-the-certificate-information-to-the-service-fabric-cluster-resource"></a>Lägg till certifikatinformationen till Service Fabric-klusterresursen:
```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  "location": "[parameters('clusterLocation')]",
  "dependsOn": [
    "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
  ],
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStoreValue')]"
    },
    ...
  }
}
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Lägg till Azure AD-konfiguration för att använda Azure AD för klientåtkomst

Du lägger till Azure AD-konfiguration s ett kluster Resource Manager-mall genom att referera till nyckelvalvet som innehåller certifikatnycklarna. Lägg till dessa Azure AD-parametrar och värden i en Resource Manager parametrar mallfil (azuredeploy.parameters.json).

```json
{
  "apiVersion": "[variables('sfrpApiVersion')]",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    "certificate": {
      "thumbprint": "[parameters('clusterCertificateThumbprint')]",
      "x509StoreName": "[parameters('clusterCertificateStorevalue')]"
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

### <a name="populate-the-parameter-file-with-the-values"></a>Fyll i parameterfilen med värden.
Använd slutligen utdatavärden från nyckelvalvet och Azure AD powershell-kommandon för att fylla i parameterfilen:

Om du planerar att använda Azure service fabric RM powershell-moduler sedan behöver du inte fylla certifikatinformationen klustret, om du vill genereras self signerat certifikat för klustret säkerhet, hålla dem precis som null. 

> [!NOTE]
> Att hämta och fylla i dessa tom parametervärden RM-moduler matchar parametrar namnen mycket namnen nedan
>

```json
        "clusterCertificateThumbprint": {
            "value": ""
        },
        "clusterCertificateUrlValue": {
            "value": ""
        },
        "sourceVaultvalue": {
            "value": ""
        },
```

Om du använder certifikat för programmet eller använder ett befintligt kluster som du har laddat upp till keyvault, måste du hämta den här informationen och fylla det 

RM-moduler har inte möjlighet att geneate Azure AD-konfiguration för dig. Så om du planerar att använda Azure AD för klientåtkomst, måste du fylla i den.

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
Använd följande PowerShell-kommando för att testa Resource Manager-mall med en parameterfil:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Om du stöter på problem och få kryptiskt meddelanden, Använd ”-Debug” som ett alternativ.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug
```

Följande diagram illustrerar där ditt nyckelvalv och Azure AD-konfiguration passar i Resource Manager-mall.

![Hanteraren för filserverresurser beroendekarta för anslutningar][cluster-security-arm-dependency-map]

## <a name="create-the-cluster-using-azure-resource-template"></a>Skapa klustret med hjälp av Azure-resurs-mall 

Du kan nu distribuera kluster med hjälp av stegen som beskrivs tidigare i dokumentet eller om du har värdena i parameterfilen fyllts, sedan du är nu redo att skapa klustret med hjälp av [Azure-resurs malldistribution] [ resource-group-template-deploy] direkt.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

Om du stöter på problem och få kryptiskt meddelanden, Använd ”-Debug” som ett alternativ.


<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Tilldela användare till roller
När du har skapat de program som ska representera klustret tilldelar användarna till de roller som stöds av Service Fabric: skrivskyddade och administratör. Du kan tilldela roller med hjälp av den [Azure-portalen][azure-portal].

1. Gå till din klient i Azure-portalen och väljer sedan **program**.
2. Välj webbprogram som har ett namn som `myTestCluster_Cluster`.
3. Klicka på den **användare** fliken.
4. Välj en användare att tilldela och klicka sedan på den **tilldela** längst ned på skärmen.

    ![Tilldela användare till roller knappen][assign-users-to-roles-button]
5. Välj rollen för att tilldela användaren.

    ![”Tilldela användare” dialogrutan][assign-users-to-roles-dialog]

> [!NOTE]
> Mer information om roller i Service Fabric finns [rollbaserad åtkomstkontroll för Service Fabric-klienter](service-fabric-cluster-security-roles.md).
>
>


## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Felsöka hjälp med att konfigurera Azure Active Directory
Konfigurera Azure AD och använder den, kan vara en utmaning, så här följer några tips på vad du kan göra för att felsöka problemet.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer uppmanar dig att välja ett certifikat
#### <a name="problem"></a>Problem
När du lyckas logga in till Azure AD i Service Fabric Explorer webbläsaren tillbaka till startsidan men uppmanas du att välja ett certifikat.

![Dialogrutan för SFX-certifikat][sfx-select-certificate-dialog]

#### <a name="reason"></a>Orsak
Användaren är inte tilldelad en roll i Azure AD-programmet för klustret. Azure AD-autentiseringen misslyckas därför på Service Fabric-klustret. Service Fabric Explorer faller tillbaka till certifikatautentisering.

#### <a name="solution"></a>Lösning
Följ instruktionerna för att konfigurera Azure AD och tilldela användarroller. Dessutom vi rekommenderar att du aktiverar ”Användartilldelning krävs för att komma åt appen”, som `SetupApplications.ps1` har.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Anslutning med PowerShell misslyckas med felmeddelandet: ”de angivna autentiseringsuppgifterna är ogiltiga”
#### <a name="problem"></a>Problem
När du använder PowerShell för att ansluta till klustret med hjälp av ”AzureActiveDirectory” säkerhetsläget när du loggar in har i Azure AD, om anslutningen misslyckas med felmeddelandet: ”de angivna autentiseringsuppgifterna är ogiltiga”.

#### <a name="solution"></a>Lösning
Den här lösningen är samma som det föregående.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer returnerar ett fel när du loggar in: ”AADSTS50011”
#### <a name="problem"></a>Problem
När du försöker logga in på Azure AD i Service Fabric Explorer sidan returnerar ett fel ”: AADSTS50011: svarsadressen &lt;url&gt; matchar inte reply-adresser som har konfigurerats för programmet: &lt;guid&gt;”.

![SFX svarsadressen matchar inte][sfx-reply-address-not-match]

#### <a name="reason"></a>Orsak
Klustret (webb) som representerar Service Fabric Explorer försöker autentisera mot Azure AD och som en del av begäran ger den returnera omdirigerings-URL. Men URL-Adressen ingår inte i Azure AD-programmet **REPLY URL** lista.

#### <a name="solution"></a>Lösning
På den **konfigurera** fliken i klustret (webb)-programmet att lägga till URL: en för Service Fabric Explorer till den **REPLY URL** listan eller ersätta ett av objekten i listan. När du är klar kan du spara ändringen.

![Url till webbprogram svar][web-application-reply-url]

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Ansluta till klustret med hjälp av Azure AD-autentisering via PowerShell
Använd följande PowerShell-kommandot exempel för att ansluta Service Fabric-kluster:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Läs om cmdlet Connect-ServiceFabricCluster i [Connect-ServiceFabricCluster](https://msdn.microsoft.com/library/mt125938.aspx).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Kan jag återanvända samma Azure AD-klienten i flera kluster?
Ja. Men kom ihåg att lägga till URL: en för Service Fabric Explorer i ditt program i klustret (webb). Annars fungerar Service Fabric Explorer inte.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Varför måste jag göra fortfarande ett servercertifikat medan Azure AD är aktiverad?
FabricClient och FabricGateway utför en ömsesidig autentisering. Azure AD-integreringen ger en klientens identitet till servern under Azure AD-autentisering, och certifikatet används för att kontrollera serveridentitet. Mer information om Service Fabric-certifikat finns [X.509-certifikat och Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Nästa steg
Du har nu en säker kluster med Azure Active Directory med management-autentisering. Nästa [ansluta till klustret](service-fabric-connect-to-secure-cluster.md) och lära dig hur du [hantera program hemligheter](service-fabric-application-secret-management.md).


<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric
[customize-your-cluster-template]: service-fabric-cluster-creation-via-arm.md#Create-a-Service-Fabric-cluster- Resource-Manager-template

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

