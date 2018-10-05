---
title: Distribuera säkra Service Fabric-kluster i Azure Stack | Microsoft Docs
description: Lär dig hur du distribuerar säkra Service Fabric-kluster i Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/12/2018
ms.author: mattbriggs
ms.reviewer: shnatara
ms.openlocfilehash: d402b2bcd5187cbb6ece78d7e981068c279c1f75
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804437"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Distribuera ett Service Fabric-kluster i Azure Stack

Använd den **Service Fabric-kluster** objekt från Azure Marketplace för att distribuera säkra Service Fabric-kluster i Azure Stack. 

Mer information om hur du arbetar med Service Fabric finns i [översikt av Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) och [säkerhetsscenarier för Service Fabric-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security), i Azure-dokumentationen.

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att distribuera Service Fabric-klustret:
1. **Klustercertifikat**  
   Det här är X.509-servercertifikat som du lägger till i KeyVault vid distribution av Service Fabric. 
   - Den **CN** på det här certifikatet måste matcha det fullständigt kvalificerade domännamn (FQDN) för Service Fabric-klustret som du skapar. 
   - Certifikatformatet måste vara PFX, som krävs för både offentliga och privata nycklar. 
   Se [krav](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) för att skapa det här certifikatet för serversidan.

    > [!NOTE]  
    > Du kan använda ett självsignerat certifikat inplace för certifikatet för x.509 för testning. Självsignerade certifikat behöver inte matcha det fullständiga Domännamnet för klustret.

1.  **Admin-klientcertifikatet** är detta certifikat som klienten använder för att autentisera till Service Fabric-kluster, vilket kan vara självsignerade. Se [krav](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) för att skapa det här klientcertifikatet.

1.  **Följande objekt måste vara tillgängliga i Azure Stack Marketplace:**
     - **Windows Server 2016** – mallen använder Windows Server 2016-avbildning för att skapa klustret.  
     - **Kundskriptstillägget** -tillägg för virtuell dator från Microsoft.  
     - **PowerShell Desired Configuration scenen** -tillägg för virtuell dator från Microsoft.


## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault
Om du vill distribuera ett Service Fabric-kluster, måste du ange rätt KeyVault *hemlig identifierare* eller URL: en för Service Fabric-klustret. Azure Resource Manager-mallen tar en KeyVault som indata och hämtar sedan klustercertifikatet under installationen av Service Fabric-klustret. 

> [!IMPORTANT]  
> Du måste använda PowerShell för att lägga till en hemlighet i KeyVault för användning med Service Fabric. Använd inte på portalen.  

Använd följande skript för att skapa Nyckelvalvet och lägga till den *klustercertifikat* till den. (Se den [krav](#prerequisites).) Innan du kör skriptet, granska exempelskript och uppdatera de angivna parametrarna som matchar din miljö. Det här skriptet kommer också att utdata de värden som du ska ange i Azure Resource Manager-mallen. 

> [!TIP]  
> Innan skriptet kan lyckas, måste det finnas ett erbjudande till allmänheten som innehåller tjänster för beräkning, nätverk, lagring och KeyVault. 

  ```PowerShell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


Mer information finns i [hantera KeyVault på Azure Stack med PowerShell](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-kv-manage-powershell).

## <a name="deploy-the-marketplace-item"></a>Distribuera Marketplace-objekt

1. I användarportalen, går du till **+ skapa en resurs** > **Compute** > **Service Fabric-kluster**. 

   ![Välj Service Fabric-kluster](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

1. För varje sida som *grunderna*, Fyll i formuläret för distribution. Använd standardinställningarna om du inte är säker på ett värde. Välj **OK** att gå vidare till nästa sida:

   ![Grundläggande inställningar](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

1. På den *nätverksinställningar* kan du ange specifika portar som ska öppnas för dina program:

   ![Nätverksinställningar](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

1. På den *Security* lägger du till de värden som du fick från [skapar Azure KeyVault](#add-a-secret-to-key-vault) och ladda upp hemligheten.

   För den *Admin Klientcertifikatets tumavtryck*, ange certifikatets tumavtryck i den *Admin klientcertifikat*. (Se den [krav](#prerequisites).)
   
   - Key Vault källa: Ange hela *keyVault-id* sträng från Skriptresultat. 
   - Kluster-URL för certifikat: Ange hela URL: en från den *hemlighet Id* från skript-resultaten. 
   - Kluster-tumavtryck för certifikat: Ange den *kluster certifikatets tumavtryck* från skript-resultaten.
   - Administratörsklient Certifikattumavtryck: Ange den *Admin Klientcertifikatets tumavtryck* du har skapat i förutsättningarna. 

   ![Skriptutdata](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Säkerhet](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

1. Slutför guiden och välj sedan **skapa** att distribuera Service Fabric-kluster.



## <a name="access-the-service-fabric-cluster"></a>Få åtkomst till Service Fabric-kluster
Du kan komma åt Service Fabric-kluster med hjälp av Service Fabric Explorer eller Service Fabric PowerShell.


### <a name="use-service-fabric-explorer"></a>Använda Service Fabric Explorer
1.  Verifiera att webbläsaren har åtkomst till ditt klientcertifikat för administratör och kan autentisera till Service Fabric-klustret.  

    a. Öppna Internet Explorer och gå till **Internetalternativ** > **innehåll** > **certifikat**.
  
    b. Certifikat, Välj **Import** att starta den *guiden Importera certifikat*, och klicka sedan på **nästa**. På den *fil som ska importeras* klickar du på **Bläddra**, och välj den **Admin klientcertifikat** du angav i Azure Resource Manager-mallen.
        
       > [!NOTE]  
       > Det här certifikatet är inte det klustercertifikat som tidigare har lagts till i KeyVault.  

    c. Kontrollera att du har ”Personal Information Exchange” valde i listrutan tillägget i Utforskaren-fönstret.  

       ![Personligt informationsutbyte](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. På den *Certificate Store* väljer **personliga**, och slutför sedan guiden.  
       ![Certifikatarkiv](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. Hitta FQDN för Service Fabric-klustret:  

    a. Gå till den resursgrupp som är associerad med Service Fabric-kluster och leta upp den *offentliga IP-adressen* resurs. Markera objektet som är associerade med den offentliga IP-adressen för att öppna den *offentliga IP-adressen* bladet.  

      ![Offentlig IP-adress](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. På bladet offentliga IP-adress visas det fullständiga Domännamnet som *DNS-namnet*.  

      ![DNS-namn](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. Du hittar Webbadressen för Service Fabric Explorer och slutpunkten för klientanslutning, granskar du resultaten av malldistributionen.

1. I webbläsaren går du till https://*FQDN*: 19080. Ersätt *FQDN* med FQDN för Service Fabric-klustret från steg 2.   
   Om du har använt ett självsignerat certifikat, får du en varning om att anslutningen inte är säker. Om du vill fortsätta till webbplatsen, Välj **mer Information**, och sedan **går du vidare till webbsidan**. 

1. Du måste välja ett certifikat som ska användas för att autentisera till webbplatsen. Välj **fler alternativ**, väljer du lämpligt certifikat och klicka sedan på **OK** att ansluta till Service Fabric Explorer. 

   ![Autentisera](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Använda Service Fabric PowerShell

1. Installera den *Microsoft Azure Service Fabric SDK* från [förbereda utvecklingsmiljön i Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) i Azure Service Fabric-dokumentationen.  

1. När installationen är klar, kan du konfigurera systemets miljövariabler för att säkerställa att Service Fabric-cmdlets är tillgängliga från PowerShell.  
    
    a. Gå till **Kontrollpanelen** > **System och säkerhet** > **System**, och välj sedan **avancerade systeminställningar**.  
    
      ![På Kontrollpanelen](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. På den **Avancerat** fliken *Systemegenskaper*väljer **miljövariabler**.  

    c. För *systemvariabler*, redigera **sökväg** och se till att **C:\\programfiler\\Microsoft Service Fabric\\bin\\Fabric \\Fabric.Code** är överst i listan över miljövariabler.  

      ![Miljö variabellista](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. När du har ändrat ordning på miljövariablerna, starta om PowerShell och kör sedan följande PowerShell-skript för att få åtkomst till Service Fabric-klustret:

   ````PowerShell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ````
   
   > [!NOTE]  
   > Det finns inga *https://* före namnet på klustret i skriptet. Port 19000 krävs.
 
