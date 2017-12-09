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
ms.date: 06/22/2017
ms.author: chackdan
ms.openlocfilehash: 47152d05eb7e31e7fe1f35e33a10fe8e903e21e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-service-fabric-cluster-by-using-azure-resource-manager"></a>Skapa ett Service Fabric-kluster med hjälp av Azure Resource Manager
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Den här stegvisa guiden vägleder dig genom att skapa en säker Azure Service Fabric-kluster i Azure med hjälp av Azure Resource Manager. Vi bekräftar att artikeln är långt. Dock om du redan är väl bekanta dig med innehållet, måste du följa varje steg noggrant.

Guiden innehåller följande procedurer:

* Ställa in en Azure key vault för att ladda upp certifikat för kluster- och säkerhet
* Skapar en skyddad kluster i Azure med hjälp av Azure Resource Manager
* Autentisering av användare med hjälp av Azure Active Directory (Azure AD) för hantering av kluster

En säker klustret är ett kluster som förhindrar obehörig åtkomst till hanteringsåtgärder. Detta inkluderar distribution, uppgradera och ta bort program, tjänster och de data som de innehåller. Ett oskyddat klustret är ett kluster alla kan ansluta till när som helst och utföra hanteringsåtgärder. Men det är möjligt att skapa ett oskyddade kluster, rekommenderar vi starkt att du skapar en säker klustret redan från början. Eftersom ett oskyddade kluster inte kan säkras senare, måste du skapa ett nytt kluster.

Konceptet för att skapa skyddade kluster är densamma, oavsett om de är Linux eller Windows-kluster. Mer information och hjälp skript för att skapa säkra Linux-kluster, se [att skapa skyddade kluster på Linux](#secure-linux-clusters).

## <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto
Den här guiden använder [Azure PowerShell][azure-powershell]. När du startar en ny PowerShell-session, logga in på ditt Azure-konto och välja din prenumeration innan du kan köra kommandon för Azure.

Logga in på ditt Azure-konto:

```powershell
Login-AzureRmAccount
```

Välj din prenumeration:

```powershell
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="set-up-a-key-vault"></a>Ställ in ett nyckelvalv
Det här avsnittet beskrivs skapa nyckelvalvet för Service Fabric-kluster i Azure och för Service Fabric-program. En fullständig guide till Azure Key Vault finns i den [Key Vault Kom igång med][key-vault-get-started].

Service Fabric använder X.509-certifikat för att skydda ett kluster och säkerhetsfunktioner för programmet. Du kan använda Nyckelvalv för att hantera certifikat för Service Fabric-kluster i Azure. När ett kluster distribueras i Azure, Azure-resursprovider som ansvarar för att skapa Service Fabric-kluster hämtar certifikat från Nyckelvalvet och installerar dem på klustret virtuella datorer.

Följande diagram illustrerar förhållandet mellan Azure Key Vault, Service Fabric-kluster och Azure-resursprovider som använder certifikat som lagras i en nyckelvalvet när den skapar ett kluster:

![Diagram över certifikatinstallation][cluster-security-cert-installation]

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
Det första steget är att skapa en resursgrupp för nyckelvalvet. Vi rekommenderar att du placera nyckelvalvet i sin egen resursgruppen. Den här åtgärden kan du ta bort beräkning och lagring resursgrupper, inklusive resursgruppen som innehåller Service Fabric-klustret utan att förlora dina nycklar och hemligheter. Resursgruppen som innehåller nyckelvalvet _måste vara i samma region_ som klustret som använder den.

Om du planerar att distribuera kluster i flera områden, föreslår vi att du namnger resursgruppen och nyckeln valvet på ett sätt som anger vilken region som den tillhör.  

```powershell

    New-AzureRmResourceGroup -Name westus-mykeyvault -Location 'West US'
```
Resultatet bör se ut så här:

```powershell

    WARNING: The output object type of this cmdlet is going to be modified in a future release.

    ResourceGroupName : westus-mykeyvault
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    ResourceId        : /subscriptions/<guid>/resourceGroups/westus-mykeyvault

```
<a id="new-key-vault"></a>

### <a name="create-a-key-vault-in-the-new-resource-group"></a>Skapa en nyckelvalvet i den nya resursgruppen
Nyckelvalvet _måste vara aktiverat för distribution_ att compute-resursprovidern så att hämta certifikat från det och installera det på instanser för virtuella datorer:

```powershell

    New-AzureRmKeyVault -VaultName 'mywestusvault' -ResourceGroupName 'westus-mykeyvault' -Location 'West US' -EnabledForDeployment

```

Resultatet bör se ut så här:

```powershell

    Vault Name                       : mywestusvault
    Resource Group Name              : westus-mykeyvault
    Location                         : West US
    Resource ID                      : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault
    Vault URI                        : https://mywestusvault.vault.azure.net
    Tenant ID                        : <guid>
    SKU                              : Standard
    Enabled For Deployment?          : False
    Enabled For Template Deployment? : False
    Enabled For Disk Encryption?     : False
    Access Policies                  :
                                       Tenant ID                :    <guid>
                                       Object ID                :    <guid>
                                       Application ID           :
                                       Display Name             :    
                                       Permissions to Keys      :    get, create, delete, list, update, import, backup, restore
                                       Permissions to Secrets   :    all


    Tags                             :
```
<a id="existing-key-vault"></a>

## <a name="use-an-existing-key-vault"></a>Använd en befintlig nyckelvalv

Att använda en befintlig nyckelvalv du _måste aktivera den för distribution_ att compute-resursprovidern så att hämta certifikat från det och installera den på klusternoder:

```powershell

Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

```

<a id="add-certificate-to-key-vault"></a>

## <a name="add-certificates-to-your-key-vault"></a>Lägga till certifikat i nyckelvalvet

Certifikat används i Service Fabric till att autentisera och kryptera olika delar av ett kluster och de program som körs där. Mer information om hur du använder certifikat i Service Fabric finns [säkerhetsscenarier för Service Fabric-klustret][service-fabric-cluster-security].

### <a name="cluster-and-server-certificate-required"></a>Kluster- och certifikat (krävs)
Detta certifikat krävs för att skydda ett kluster och förhindra obehörig åtkomst till den. Det ger säkerhet i klustret på två sätt:

* Autentisering för klustret: autentiserar nod till nod kommunikation för klustret. Endast noder som kan bevisa sin identitet med det här certifikatet kan ansluta till klustret.
* Serverautentisering: verifierar att klustret management slutpunkter management-klienten så att klienten management vet pratar till verkliga klustret. Det här certifikatet ger också en SSL för HTTPS-hanterings-API och för Service Fabric Explorer via HTTPS.

Om du vill hantera dessa ändamål måste certifikatet uppfylla följande krav:

* Certifikatet måste innehålla en privat nyckel.
* Certifikatet måste skapas för nyckelutbyte, som kan exporteras till en Personal Information Exchange (.pfx)-fil.
* Certifikatets ämnesnamn måste matcha den domän som du använder för att få åtkomst till Service Fabric-klustret. Den här matchar krävs att tillhandahålla en SSL för klustrets HTTPS management slutpunkter och Service Fabric Explorer. Du kan skaffa ett SSL-certifikat från en certifikatutfärdare (CA) för det. cloudapp.azure.com domän. Du måste skaffa ett anpassat domännamn för klustret. När du begär ett certifikat från en Certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som du använder för klustret.

### <a name="application-certificates-optional"></a>Certifikat för programmet (valfritt)
Valfritt antal ytterligare certifikat kan installeras på ett kluster av säkerhetsskäl för programmet. Tänk på applikationssäkerhets-scenarier som kräver ett certifikat installeras på noderna, som innan du skapar klustret:

* Kryptering och dekryptering av konfigurationsvärden för programmet.
* Kryptering av data mellan noderna under replikeringen.

### <a name="formatting-certificates-for-azure-resource-provider-use"></a>Formatering certifikat för användning av Azure resource provider
Du kan lägga till och använda privata nyckelfiler (.pfx) direkt via nyckelvalvet. Azure compute-resursprovidern kräver dock nycklar lagras i en särskild JavaScript Object Notation (JSON)-format. Formatet som innehåller PFX-filen som en base 64-kodad sträng och lösenordet för privata nyckeln. Om du vill anpassa dessa krav ska nycklarna placeras i en JSON-sträng och sedan lagras som ”hemligheter” i nyckelvalvet.

Att göra den här processen enklare, en [PowerShell-modulen finns på GitHub][service-fabric-rp-helpers]. Om du vill använda modulen, gör du följande:

1. Hämta hela innehållet på lagringsplatsen till en lokal katalog.
2. Gå till den lokala katalogen.
2. Importera modulen ServiceFabricRPHelpers i PowerShell-fönstret:

```powershell

 Import-Module "C:\..\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

```

Den `Invoke-AddCertToKeyVault` kommandot i PowerShell-modulen automatiskt formaterar en certifikatets privata nyckel till en JSON-sträng och överförs till nyckelvalvet. Använd kommandot för att lägga till kluster-certifikat och några ytterligare certifikat i nyckelvalvet. Upprepa det här steget för några ytterligare certifikat som du vill installera i klustret.

#### <a name="uploading-an-existing-certificate"></a>Ladda upp ett befintligt certifikat

```powershell

 Invoke-AddCertToKeyVault -SubscriptionId <guid> -ResourceGroupName westus-mykeyvault -Location "West US" -VaultName mywestusvault -CertificateName mycert -Password "<password>" -UseExistingCertificate -ExistingPfxFilePath "C:\path\to\mycertkey.pfx"

```

Om du får ett felmeddelande som visas här är oftast det att det finns en konflikt för resurs-URL. Ändra namnet på nyckelvalv för att lösa konflikten.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

Efter att konflikten löses utdata ska se ut så här:

```

    Switching context to SubscriptionId <guid>
    Ensuring ResourceGroup westus-mykeyvault in West US
    WARNING: The output object type of this cmdlet is going to be modified in a future release.
    Using existing value mywestusvault in West US
    Reading pfx file from C:\path\to\key.pfx
    Writing secret to mywestusvault in vault mywestusvault


Name  : CertificateThumbprint
Value : E21DBC64B183B5BF355C34C46E03409FEEAEF58D

Name  : SourceVault
Value : /subscriptions/<guid>/resourceGroups/westus-mykeyvault/providers/Microsoft.KeyVault/vaults/mywestusvault

Name  : CertificateURL
Value : https://mywestusvault.vault.azure.net:443/secrets/mycert/4d087088df974e869f1c0978cb100e47

```

>[!NOTE]
>Du måste de tre föregående strängar, CertificateThumbprint, SourceVault och CertificateURL att ställa in en säker Service Fabric-klustret och att hämta programmet certifikat som du kan använda för programsäkerhet. Om du inte sparar strängar kan det vara svårt att hämta dem genom att fråga nyckelvalvet senare.

<a id="add-self-signed-certificate-to-key-vault"></a>

#### <a name="creating-a-self-signed-certificate-and-uploading-it-to-the-key-vault"></a>Skapa ett självsignerat certifikat och överföra den till nyckelvalvet

Hoppa över det här steget om du redan har överfört din certifikat till nyckelvalvet. Det här steget är för att generera ett nytt självsignerat certifikat och överföra den till nyckelvalvet. När du ändrar parametrar i följande skript och kör det ska efterfrågas ett lösenord för certifikatet.  

```powershell

$ResourceGroup = "chackowestuskv"
$VName = "chackokv2"
$SubID = "6c653126-e4ba-42cd-a1dd-f7bf96ae7a47"
$locationRegion = "westus"
$newCertName = "chackotestcertificate1"
$dnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$localCertPath = "C:\MyCertificates" # location where you want the .PFX to be stored

 Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResourceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath

```

Om du får ett felmeddelande som visas här är oftast det att det finns en konflikt för resurs-URL. Lös konflikten genom att ändra nyckelvalv namn, RG namn och så vidare.

```
Set-AzureKeyVaultSecret : The remote name could not be resolved: 'westuskv.vault.azure.net'
At C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1:440 char:11
+ $secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $Certif ...
+           ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Set-AzureKeyVaultSecret], WebException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.KeyVault.SetAzureKeyVaultSecret

```

Efter att konflikten löses utdata ska se ut så här:

```
PS C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers> Invoke-AddCertToKeyVault -SubscriptionId $SubID -ResourceGroupName $ResouceGroup -Location $locationRegion -VaultName $VName -CertificateName $newCertName -Password $certPassword -CreateSelfSignedCertificate -DnsName $dnsName -OutputPath $localCertPath
Switching context to SubscriptionId 6c343126-e4ba-52cd-a1dd-f8bf96ae7a47
Ensuring ResourceGroup chackowestuskv in westus
WARNING: The output object type of this cmdlet will be modified in a future release.
Creating new vault westuskv1 in westus
Creating new self signed certificate at C:\MyCertificates\chackonewcertificate1.pfx
Reading pfx file from C:\MyCertificates\chackonewcertificate1.pfx
Writing secret to chackonewcertificate1 in vault westuskv1


Name  : CertificateThumbprint
Value : 96BB3CC234F9D43C25D4B547sd8DE7B569F413EE

Name  : SourceVault
Value : /subscriptions/6c653126-e4ba-52cd-a1dd-f8bf96ae7a47/resourceGroups/chackowestuskv/providers/Microsoft.KeyVault/vaults/westuskv1

Name  : CertificateURL
Value : https://westuskv1.vault.azure.net:443/secrets/chackonewcertificate1/ee247291e45d405b8c8bbf81782d12bd

```

>[!NOTE]
>Du måste de tre föregående strängar, CertificateThumbprint, SourceVault och CertificateURL att ställa in en säker Service Fabric-klustret och att hämta programmet certifikat som du kan använda för programsäkerhet. Om du inte sparar strängar kan det vara svårt att hämta dem genom att fråga nyckelvalvet senare.

 Du bör nu ha följande element på plats:

* Resursgruppen för nyckelvalv.
* Nyckelvalvet och dess URL (kallas SourceVault i föregående PowerShell utdata).
* Certifikat för serverautentisering i klustret och dess URL: en i nyckelvalvet.
* Programcertifikat och deras URL: er i nyckelvalvet.


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

    Du uppmanas att logga in på ett konto som har administratörsbehörighet för Azure AD-klient. När du loggar in skapar skriptet webb- och interna program för att representera Service Fabric-klustret. Om du tittar på klientens program i den [klassiska Azure-portalen][azure-classic-portal], bör du se två nya poster:

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

## <a name="create-a-service-fabric-cluster-resource-manager-template"></a>Skapa en mall för Service Fabric-kluster Resource Manager
I det här avsnittet används utdata av föregående PowerShell-kommandon i Service Fabric-kluster Resource Manager-mall.

Exempel Resource Manager-mallar är tillgängliga i den [Azure Snabbkurs mallgalleriet på GitHub][azure-quickstart-templates]. Dessa mallar kan användas som en startpunkt för mallen för klustret.

### <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen
Den här guiden använder den [säker kluster med 5] [ service-fabric-secure-cluster-5-node-1-nodetype] exempel mallen och mallparametrar. Hämta `azuredeploy.json` och `azuredeploy.parameters.json` till din dator och öppna filer i valfri textredigerare.

### <a name="add-certificates"></a>Lägg till certifikat
Du lägga till certifikat i ett kluster Resource Manager-mall genom att referera till nyckelvalvet som innehåller certifikatnycklarna. Vi rekommenderar att du placera nyckelvalvet värden i en parameterfil för Resource Manager-mall. Gör det håller resurshanteraren mallfilen återanvändbara och utan värden specifika till en distribution.

#### <a name="add-all-certificates-to-the-virtual-machine-scale-set-osprofile"></a>Lägg till alla certifikat i virtual machine scale set osProfile
Alla certifikat som installeras i klustret måste konfigureras i avsnittet osProfile i scale set-resurs (Microsoft.Compute/virtualMachineScaleSets). Den här åtgärden instruerar resursprovidern att installera certifikatet på virtuella datorer. Den här installationen innehåller både certifikatet kluster och alla program security-certifikat som du planerar att använda för dina program:

```json
{
  "apiVersion": "2016-03-30",
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

##### <a name="virtual-machine-scale-set-resource"></a>Virtuella skaluppsättning för resursen:
```json
{
  "apiVersion": "2016-03-30",
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

##### <a name="service-fabric-resource"></a>Service Fabric-resurs:
```json
{
  "apiVersion": "2016-03-01",
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

### <a name="insert-azure-ad-configuration"></a>Infoga Azure AD-konfiguration
Azure AD-konfiguration som du skapade tidigare kan infogas direkt i Resource Manager-mall. Vi rekommenderar dock att du först extrahera värdena i en fil med parametrar att hålla resurshanteraren mall kan återanvändas och fria från värden som är specifika för en distribution.

```json
{
  "apiVersion": "2016-03-01",
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

### < en ”konfigurera arm” ></a>konfigurera Hanteraren för filserverresurser mallparametrar
<!--- Loc Comment: It seems that <a "configure-arm" > must be replaced with <a name="configure-arm"></a> since the link seems not to be redirecting correctly --->
Använd slutligen utdatavärden från nyckelvalvet och Azure AD PowerShell-kommandon för att fylla i parameterfilen:

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
Du bör nu ha följande element på plats:

* Nyckelvalv resursgruppen.
  * Nyckelvalv
  * Klustret certifikat för serverautentisering
  * Data chiffrering av certifikat
* Azure Active Directory-klient
  * Azure AD-program för Webbaserad hantering och Service Fabric Explorer
  * Azure AD-program för interna klienthantering
  * Användare och deras tilldelade roller
* Service Fabric-kluster Resource Manager-mall
  * Certifikat konfigureras via nyckelvalv
  * Azure Active Directory konfigurerat

Följande diagram illustrerar där ditt nyckelvalv och Azure AD-konfiguration passar i Resource Manager-mall.

![Hanteraren för filserverresurser beroendekarta för anslutningar][cluster-security-arm-dependency-map]

## <a name="create-the-cluster"></a>Skapa klustret
Du är nu redo att skapa klustret med hjälp av [Azure-resurs malldistribution][resource-group-template-deploy].

#### <a name="test-it"></a>testa den
Använd följande PowerShell-kommando för att testa Resource Manager-mall med en fil med parametrar:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

#### <a name="deploy-it"></a>distribuera den
Om hanteraren för filserverresurser mallen testet lyckas, kan du använda följande PowerShell-kommando för att distribuera Resource Manager-mall med en fil med parametrar:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName "myresourcegroup" -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json
```

<a name="assign-roles"></a>

## <a name="assign-users-to-roles"></a>Tilldela användare till roller
När du har skapat de program som ska representera klustret tilldelar användarna till de roller som stöds av Service Fabric: skrivskyddade och administratör. Du kan tilldela roller med hjälp av den [klassiska Azure-portalen][azure-classic-portal].

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

 <a name="secure-linux-clusters"></a>
 <!--- Loc Comment: It seems that letter S in cluster was missing, which caused the wrong redirection of the link --->

## <a name="create-secure-clusters-on-linux"></a>Skapa skyddade kluster på Linux
Du kan förenkla processen har vi angett ett [helper skriptet](http://github.com/ChackDan/Service-Fabric/tree/master/Scripts/CertUpload4Linux). Innan du använder skriptet helper se till att du redan har Azure-kommandoradsgränssnittet (CLI) installerad och den är i din sökväg. Kontrollera att skriptet har behörighet att köra genom att köra `chmod +x cert_helper.py` när du har hämtat den. Det första steget är att logga in på ditt Azure-konto med hjälp av CLI med den `azure login` kommando. När du loggar in på ditt Azure-konto, använder du helper skriptet med din CA-signerat certifikat, så som visas i följande kommando:

```sh
./cert_helper.py [-h] CERT_TYPE [-ifile INPUT_CERT_FILE] [-sub SUBSCRIPTION_ID] [-rgname RESOURCE_GROUP_NAME] [-kv KEY_VAULT_NAME] [-sname CERTIFICATE_NAME] [-l LOCATION] [-p PASSWORD]
```

Parametern - ifile kan ta en .pfx-fil eller en PEM-filen som indata med certifikattyp (pfx eller pem eller ss om det är ett självsignerat certifikat).
I parametern -h skriver ut den hjälptext som visas.


Det här kommandot returnerar följande tre strängar som utdata:

* SourceVaultID, vilket är ID för nya KeyVault ResourceGroup den skapas automatiskt
* CertificateUrl för åtkomst till certifikatet
* CertificateThumbprint som används för autentisering

I följande exempel visas hur du använder kommandot:

```sh
./cert_helper.py pfx -sub "fffffff-ffff-ffff-ffff-ffffffffffff"  -rgname "mykvrg" -kv "mykevname" -ifile "/home/test/cert.pfx" -sname "mycert" -l "East US" -p "pfxtest"
```
Kör kommandot ovan ger dig de tre strängarna på följande sätt:

```sh
SourceVault: /subscriptions/fffffff-ffff-ffff-ffff-ffffffffffff/resourceGroups/mykvrg/providers/Microsoft.KeyVault/vaults/mykvname
CertificateUrl: https://myvault.vault.azure.net/secrets/mycert/00000000000000000000000000000000
CertificateThumbprint: 0xfffffffffffffffffffffffffffffffffffffffff
```

Certifikatets ämnesnamn måste matcha den domän som du använder för att få åtkomst till Service Fabric-klustret. Matchningen krävs för att tillhandahålla en SSL för klustrets HTTPS management slutpunkter och Service Fabric Explorer. Du kan skaffa ett SSL-certifikat från en Certifikatutfärdare för den `.cloudapp.azure.com` domän. Du måste skaffa ett anpassat domännamn för klustret. När du begär ett certifikat från en Certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som du använder för klustret.

Dessa ämnesnamn är transaktioner som du behöver skapa en säker Service Fabric-klustret (utan Azure AD), enligt beskrivningen i [konfigurera Hanteraren för filserverresurser mallparametrar](#configure-arm). Du kan ansluta till det säkra klustret genom att följa anvisningarna för [autentisera klientåtkomst till ett kluster](service-fabric-connect-to-secure-cluster.md). Linux-kluster stöder inte Azure AD-autentisering. Du kan tilldela roller admin och klienten enligt beskrivningen i den [tilldela roller till användare](#assign-roles) avsnitt. Du måste ange tumavtryck för certifikatet för autentisering när du anger admin och klienten roller för en Linux-kluster. Ämnesnamn, ange inte eftersom ingen verifiering av certifikatkedjan eller återkallade utförs.

Du kan använda samma skript för att generera en om du vill använda ett självsignerat certifikat för testning. Du kan sedan överföra certifikatet till nyckelvalvet genom att ange flaggan `ss` i stället för att ange certifikatets sökväg och certifikatets namn. Se exempelvis följande kommando för att skapa och ladda upp ett självsignerat certifikat:

```sh
./cert_helper.py ss -rgname "mykvrg" -sub "fffffff-ffff-ffff-ffff-ffffffffffff" -kv "mykevname"   -sname "mycert" -l "East US" -p "selftest" -subj "mytest.eastus.cloudapp.net"
```
Det här kommandot returnerar samma tre strängar: SourceVault, CertificateUrl och CertificateThumbprint. Du kan sedan använda strängarna för att skapa både en säker Linux-kluster och en plats där det självsignerade certifikatet är placerad. Du måste det självsignerade certifikatet för att ansluta till klustret. Du kan ansluta till det säkra klustret genom att följa anvisningarna för [autentisera klientåtkomst till ett kluster](service-fabric-connect-to-secure-cluster.md).

Certifikatets ämnesnamn måste matcha den domän som du använder för att få åtkomst till Service Fabric-klustret. Matchningen krävs för att tillhandahålla en SSL för klustrets HTTPS management slutpunkter och Service Fabric Explorer. Du kan skaffa ett SSL-certifikat från en Certifikatutfärdare för den `.cloudapp.azure.com` domän. Du måste skaffa ett anpassat domännamn för klustret. När du begär ett certifikat från en Certifikatutfärdare måste certifikatets ämnesnamn matcha det anpassade domännamnet som du använder för klustret.

Du kan fylla parametrarna från helper-skriptet i Azure-portalen, enligt beskrivningen i den [skapa ett kluster i Azure portal](service-fabric-cluster-creation-via-portal.md#create-cluster-in-the-azure-portal) avsnitt.

## <a name="next-steps"></a>Nästa steg
Du har nu en säker kluster med Azure Active Directory med management-autentisering. Nästa [ansluta till klustret](service-fabric-connect-to-secure-cluster.md) och lära dig hur du [hantera program hemligheter](service-fabric-application-secret-management.md).

## <a name="troubleshoot-setting-up-azure-active-directory-for-client-authentication"></a>Felsöka ställa in Azure Active Directory för klientautentisering
Om du stöter på ett problem när du konfigurerar Azure AD för klientautentisering, granska möjliga lösningar i det här avsnittet.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer uppmanar dig att välja ett certifikat
#### <a name="problem"></a>Problem
När du lyckas logga in till Azure AD i Service Fabric Explorer webbläsaren tillbaka till startsidan men uppmanas du att välja ett certifikat.

![Dialogrutan för SFX Välj certifikat][sfx-select-certificate-dialog]

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

<!-- Links -->
[azure-powershell]:https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[key-vault-get-started]:../key-vault/key-vault-get-started.md
[aad-graph-api-docs]:https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com
[service-fabric-rp-helpers]: https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]: ../active-directory/active-directory-howto-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[azure-quickstart-templates]: https://github.com/Azure/azure-quickstart-templates
[service-fabric-secure-cluster-5-node-1-nodetype]: https://github.com/Azure/azure-quickstart-templates/blob/master/service-fabric-secure-cluster-5-node-1-nodetype/
[resource-group-template-deploy]: https://azure.microsoft.com/documentation/articles/resource-group-template-deploy/
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[cluster-security-arm-dependency-map]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-arm-dependency-map.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
[assign-users-to-roles-button]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-creation-via-arm/assign-users-to-roles.png
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-via-arm/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-via-arm/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-via-arm/web-application-reply-url.png

