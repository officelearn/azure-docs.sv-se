---
title: Distribuera en skyddad Service Fabric-kluster i Azure-stacken | Microsoft Docs
description: Lär dig hur du distribuerar en skyddad Service Fabric-kluster i Azure-stacken
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
ms.date: 05/08/2018
ms.author: mattbriggs
ms.reviewer: shnatara
ms.openlocfilehash: a88d8dd2af94ac796a3b2e3c667fd40a308f02a1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33883027"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Distribuera ett Service Fabric-kluster i Azure-stacken

Använd den **Service Fabric-kluster** objekt från Azure Marketplace för att distribuera en skyddad Service Fabric-kluster i Azure-stacken. 

Mer information om hur du arbetar med Service Fabric finns [översikt av Azure Service Frabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) och [säkerhetsscenarier för Service Fabric-klustret](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security), i Azure-dokumentationen.

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att distribuera Service Fabric-kluster:
1. **Certifikat för kluster**  
   Detta är X.509-servercertifikat som du lägger till i KeyVault när du distribuerar Service Fabric. 
   - Den **CN** på det här certifikatet måste matcha det fullständigt kvalificerade domännamn (FQDN) för Service Fabric-klustret som du skapar. 
   - Certifikatets format måste vara PFX, som båda de offentliga och privata nycklarna är obligatoriska. 
   Se [krav](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) för att skapa det här certifikatet för servern.

    > [!NOTE]  
    > Du kan använda ett självsignerat certifikat inplace för x.509-certifikatet för servern för testning. Självsignerade certifikat behöver inte matcha FQDN för klustret.

2.  **Admin klientcertifikat** detta är det certifikat som klienten använder för att autentisera till Service Fabric-kluster, vilket kan vara självsignerade. Se [krav](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) för att skapa det här certifikatet klienten.

3.  **Följande objekt måste vara tillgängligt i Azure Marketplace för Stack:**
     - **Windows Server 2016** – mallen använder Windows Server 2016-avbildningen för att skapa klustret.  
     - **Tillägget av kunden skript** -tillägg för virtuell dator från Microsoft.  
     - **PowerShell önskad steget konfiguration** -tillägg för virtuell dator från Microsoft.


## <a name="add-a-secret-to-key-vault"></a>Lägga till en hemlighet i Key Vault
Om du vill distribuera ett Service Fabric-kluster måste du ange rätt KeyVault *hemlighet identifierare* eller URL för Service Fabric-klustret. Azure Resource Manager-mallen tar en KeyVault som indata och hämtar sedan klustret certifikat under installationen av Service Fabric-klustret. 

> [!IMPORTANT]  
> Du måste använda PowerShell för att lägga till en hemlighet KeyVault för användning med Service Fabric. Använd inte portalen.  

Använd följande skript för att skapa KeyVault och lägga till den *klustret certifikat* till den. (Se den [krav](#prerequisites).) Innan du kör skriptet måste granska exempelskript och uppdatera de angivna parametrarna för att matcha din miljö. Det här skriptet kommer också utdata värden måste du ange att Azure Resource Manager-mallen. 

> [!TIP]  
> Innan skriptet kan genomföras måste det finnas en offentlig erbjudandet som innehåller tjänster för beräkning, nätverk, lagring och KeyVault. 

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


Mer information finns i [hantera KeyVault Azure stacken med PowerShell](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-kv-manage-powershell).

## <a name="deploy-the-marketplace-item"></a>Distribuera Marketplace-objektet

1. I användarportalen, går du till **ny** > **Compute** > **Service Fabric-kluster**. 

   ![Välj Service Fabric-kluster](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. För varje sida som *grunderna*, Fyll i formuläret distribution. Använd standardinställningar om du är osäker på ett värde. Välj **OK** att gå vidare till nästa sida:

   ![Grundläggande inställningar](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

3. På den *nätverksinställningar* kan du ange specifika portar som ska öppnas för dina program:

   ![Nätverksinställningar](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. På den *säkerhet* , Lägg till de värden som du har fått från [skapa Azure-KeyVault](#add-a-secret-to-key-vault) och ladda upp hemligheten.

   För den *Admin Klientcertifikatets tumavtryck*, ange certifikatets tumavtryck i *Admin klientcertifikat*. (Se den [krav](#prerequisites).)
   
   - Källan Key Vault: Ange hela *keyVault id* sträng från skript-resultaten. 
   - Kluster-URL för certifikat: Ange hela URL från den *hemlighet Id* från skript-resultaten. 
   - Klustret tumavtryck för certifikat: Ange den *klustret certifikatets tumavtryck* från skript-resultaten.
   - Administratörsklient Certifikattumavtryck: Ange den *Admin Klientcertifikatets tumavtryck* du har skapat i förutsättningarna. 

   ![Skriptutdata](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Säkerhet](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. Slutför guiden och väljer sedan **skapa** att distribuera Service Fabric-kluster.



## <a name="access-the-service-fabric-cluster"></a>Åtkomst till Service Fabric-kluster
Du kan komma åt Service Fabric-kluster med hjälp av Service Fabric-Utforskaren eller Service Fabric PowerShell.


### <a name="use-service-fabric-explorer"></a>Använda Service Fabric Explorer
1.  Verifiera att webbläsaren har åtkomst till Admin klientcertifikatet och kan autentisera till Service Fabric-klustret.  

    a. Öppna Internet Explorer och gå till **Internetalternativ** > **innehåll** > **certifikat**.
  
    b. Välj på certifikat, **importera** att starta den *guiden Importera certifikat*, och klicka sedan på **nästa**. På den *fil att importera* klickar **Bläddra**, och välj den **Admin klientcertifikat** du angav i Azure Resource Manager-mallen.
        
       > [!NOTE]  
       > Det här certifikatet är inte det kluster-certifikat som tidigare har lagts till i KeyVault.  

    c. Se till att du har ”Personal Information Exchange” markerad i listrutan tillägg i fönstret Utforskaren.  

       ![Personal information exchange](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. På den *certifikatarkiv* väljer **personliga**, och slutför sedan guiden.  
       ![Certifikatarkiv](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
2. Hitta FQDN för Service Fabric-kluster:  

    a. Gå till den resursgrupp som är associerad med Service Fabric-kluster och leta upp den *offentliga IP-adressen* resurs. Markera det objekt som är associerade med den offentliga IP-adressen för att öppna den *offentliga IP-adressen* bladet.  

      ![Offentlig IP-adress](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. I bladet offentlig IP-adress visas FQDN som *DNS-namnet*.  

      ![DNS-namn](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

3. Granska resultatet av distributionen av mallen för att hitta URL: en för Service Fabric Explorer och klienten Anslutningens slutpunkt.

4. I din webbläsare går du till https://*FQDN*: 19080. Ersätt *FQDN* med FQDN för Service Fabric-kluster från steg 2.   
   Om du har använt ett självsignerat certifikat, får du en varning om att anslutningen inte är säker. Om du vill fortsätta till webbplatsen, Välj **mer Information**, och sedan **går du vidare till webbsidan**. 

5. Du måste välja ett certifikat som ska användas för att autentisera till platsen. Välj **fler alternativ**, Välj rätt certifikat och klicka sedan på **OK** att ansluta till Service Fabric Explorer. 

   ![Autentisera](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



## <a name="use-service-fabric-powershell"></a>Använda Service Fabric PowerShell

1. Installera den *Microsoft Azure Service Fabric SDK* från [förbereda din utvecklingsmiljö i Windows](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) i Azure Service Fabric-dokumentationen.  

2. När installationen är klar kan du konfigurera systemmiljövariabler för att säkerställa att Service Fabric-cmdlets är tillgängliga från PowerShell.  
    
    a. Gå till **Kontrollpanelen** > **System och säkerhet** > **System**, och välj sedan **avancerade systeminställningar**.  
    
      ![Kontrollpanelen](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. På den **Avancerat** fliken *Systemegenskaper*väljer **miljövariabler**.  

    c. För *systemvariabler*, redigera **sökväg** och se till att **C:\\programfiler\\Microsoft Service Fabric\\bin\\Fabric \\Fabric.Code** är överst i listan över miljövariabler.  

      ![Miljö variabellista](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

3. När du har ändrat ordningen på miljövariablerna, starta om PowerShell och kör följande PowerShell-skript för att få åtkomst till Service Fabric-kluster:

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
 
