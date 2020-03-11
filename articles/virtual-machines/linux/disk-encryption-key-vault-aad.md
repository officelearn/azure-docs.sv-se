---
title: Skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD (tidigare version)
description: Den här artikeln innehåller förutsättningar för att använda Microsoft Azure Disk Encryption för virtuella IaaS-datorer.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 1fa9f4e790b49e83ed4c46e92242ff182d9a47b5
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970649"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Skapa och konfigurera ett nyckel valv för Azure Disk Encryption med Azure AD (tidigare version)

**Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure AD-programparameter för att aktivera disk kryptering för virtuella datorer. Med den nya versionen behöver du inte längre ange autentiseringsuppgifter för Azure AD under steget aktivera kryptering. Alla nya virtuella datorer måste krypteras utan Azure AD-programmets parametrar med den nya versionen. Information om hur du aktiverar disk kryptering för virtuella datorer med den nya versionen finns i [Azure Disk Encryption](disk-encryption-overview.md). Virtuella datorer som redan har krypterats med Azure AD-programparametrar stöds fortfarande och bör fortsätta att behållas med AAD-syntaxen.**

Azure Disk Encryption använder Azure Key Vault för att kontrol lera och hantera disk krypterings nycklar och hemligheter.  Mer information om nyckel valv finns i [Kom igång med Azure Key Vault](../../key-vault/key-vault-get-started.md) och [skydda nyckel valvet](../../key-vault/key-vault-secure-your-key-vault.md). 

Att skapa och konfigurera ett nyckel valv för användning med Azure Disk Encryption med Azure AD (tidigare version) omfattar tre steg:

1. Skapa ett nyckelvalv. 
2. Konfigurera en Azure AD-program och tjänstens huvudnamn.
3. Ange nyckelvalvets åtkomstprincip för Azure AD-app.
4. Ange nyckelvalv avancerade åtkomstprinciper.
 
Du kan också, om du vill, skapa eller importera en nyckel krypterings nyckel (KEK).

Anvisningar om hur du [installerar verktyg och ansluter till Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)finns i huvud avsnittet [skapa och konfigurera ett nyckel valv för Azure Disk Encryption](disk-encryption-key-vault.md) artikel.

> [!Note]
> Stegen i den här artikeln är automatiserade i [skripten för Azure Disk Encryption nödvändiga CLI-skript](https://github.com/ejarvi/ade-cli-getting-started) och [Azure Disk Encryption krav på PowerShell-skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv 
Azure Disk Encryption är integrerat med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att hjälpa dig att styra och hantera disk krypterings nycklar och hemligheter i Key Vault-prenumerationen. Du kan skapa ett nyckelvalv eller Använd en befintlig för Azure Disk Encryption. Mer information om nyckel valv finns i [Kom igång med Azure Key Vault](../../key-vault/key-vault-get-started.md) och [skydda nyckel valvet](../../key-vault/key-vault-secure-your-key-vault.md). Du kan använda en Resource Manager-mall, Azure PowerShell eller Azure CLI för att skapa ett nyckelvalv. 


>[!WARNING]
>För att se till att kryptering hemligheterna inte går över regionala gränser, måste Azure Disk Encryption Key Vault och de virtuella datorerna för att finnas i samma region. Skapa och använda ett Nyckelvalv som är i samma region som den virtuella datorn måste vara krypterade. 


### <a name="bkmk_KVPSH"></a>Skapa ett nyckel valv med PowerShell

Du kan skapa ett nyckel valv med Azure PowerShell med hjälp av cmdleten [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Ytterligare cmdlets för Key Vault finns i [AZ. nyckel valv](/powershell/module/az.keyvault/). 

1. Skapa en ny resurs grupp, om det behövs, med [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Om du vill visa en lista över data Center platser använder du [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Skapa ett nytt nyckel valv med [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Observera **valv namnet**, **resurs gruppens namn**, **resurs-ID**, **valv-URI**och **objekt-ID** som returneras för senare användning när du krypterar diskarna. 


### <a name="bkmk_KVCLI"></a>Skapa ett nyckel valv med Azure CLI
Du kan hantera ditt nyckel valv med Azure CLI med hjälp av AZ-kommandon för nyckel [valv](/cli/azure/keyvault#commands) . Om du vill skapa ett nyckel valv använder du AZ-nyckel [valv skapa](/cli/azure/keyvault#az-keyvault-create).

1. Skapa en ny resurs grupp, om det behövs, med [AZ Group Create](/cli/azure/group#az-group-create). Använd [AZ Account List-locations](/cli/azure/account#az-account-list) för att lista platser 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Skapa ett nytt nyckel valv med [AZ-valv skapa](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Observera **valv namnet** (namn), **resurs gruppens namn**, **resurs-ID** (ID), **valv-URI**och **objekt-ID** som returneras för användning senare. 

### <a name="bkmk_KVRM"></a>Skapa ett nyckel valv med en Resource Manager-mall

Du kan skapa ett nyckel valv med hjälp av [Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. I mallen för Azure snabb start klickar **du på distribuera till Azure**.
2. Välj prenumeration, resurs grupp, resurs grupp plats, Key Vault namn, objekt-ID, juridiska villkor och avtal och klicka sedan på **köp**. 


## <a name="bkmk_ADapp"></a>Konfigurera en Azure AD-App och tjänstens huvud namn 
När du behöver kryptering är aktiverat på en virtuell dator som körs i Azure, Azure Disk Encryption genererar och skriver dem till ditt nyckelvalv. Hantera krypteringsnycklar i ditt nyckelvalv kräver Azure AD-autentisering. Skapa ett Azure AD-program för detta ändamål. För autentisering kan du använda antingen autentisering baserad på klientens hemliga eller [klientbaserade Azure AD-autentisering](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="bkmk_ADappPSH"></a>Konfigurera en Azure AD-App och tjänstens huvud namn med Azure PowerShell 
Hämta och Använd [Azure AD PowerShell-modulen](/powershell/azure/active-directory/install-adv2)för att köra följande kommandon. 

1. Använd PowerShell [-cmdleten New-AzADApplication](/powershell/module/az.resources/new-azadapplication) för att skapa ett Azure AD-program. MyApplicationHomePage och MyApplicationUri kan vara alla värden som du vill.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $AzureAdApplication.ApplicationId är Azure AD-ClientID och $aadClientSecret är den klienthemlighet som du ska använda senare för att aktivera Azure Disk Encryption. Skydda Azure AD-klienthemlighet på rätt sätt. Om du kör `$azureAdApplication.ApplicationId` visas ApplicationID.


### <a name="bkmk_ADappCLI"></a>Konfigurera en Azure AD-App och tjänstens huvud namn med Azure CLI

Du kan hantera tjänstens huvud namn med Azure CLI med hjälp av [AZ AD SP](/cli/azure/ad/sp) -kommandon. Mer information finns i [skapa ett huvud namn för Azure-tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Skapa ett nytt huvudnamn för tjänsten.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  AppId returnerade är Azure AD-ClientID som används i andra kommandon. Det är också det SPN som du ska använda för az keyvault set-policy. Lösenordet är den klienthemlighet som du ska använda senare för att aktivera Azure Disk Encryption. Skydda Azure AD-klienthemlighet på rätt sätt.
 
### <a name="bkmk_ADappRM"></a>Konfigurera en Azure AD-App och tjänstens huvud namn även om Azure Portal
Använd stegen från [portalen för att skapa ett Azure Active Directory-program och tjänstens huvud namn som har åtkomst till resurs](../../active-directory/develop/howto-create-service-principal-portal.md) artikeln för att skapa ett Azure AD-program. Varje steg nedan leder dig direkt till artikelavsnittet för att slutföra. 

1. [Verifiera nödvändiga behörigheter](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Skapa ett Azure Active Directory program](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Du kan använda ett namn och inloggnings-URL som du vill ha när du skapar programmet.
3. [Hämta program-ID och autentiseringsnyckel](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - Autentiseringsnyckel är klient hemligheten och används som AadClientSecret för set-AzVMDiskEncryptionExtension. 
        - Nyckeln används av programmet som en autentiseringsuppgift för att logga in på Azure AD. Den här hemligheten kallas nycklar i Azure-portalen, men har ingen relation till nyckelvalv. Skydda den här hemligheten på rätt sätt. 
     - Program-ID: t kommer att användas senare som AadClientId för set-AzVMDiskEncryptionExtension och som ServicePrincipalName för set-AzKeyVaultAccessPolicy. 

## <a name="bkmk_KVAP"></a>Ange åtkomst princip för nyckel valvet för Azure AD-appen
Om du vill skriva kryptering hemligheter angivna Key Vault, måste Azure Disk Encryption klient-ID och Klienthemlighet för Azure Active Directory-program som har behörighet att skriva hemligheter i nyckelvalvet. 

> [!NOTE]
> Azure Disk Encryption kräver att du konfigurerar följande åtkomst principer för ditt Azure AD-klientprogram: _WrapKey_ och _Ange_ behörigheter.

### <a name="bkmk_KVAPPSH"></a>Ange åtkomst principen för nyckel valvet för Azure AD-appen med Azure PowerShell
Azure AD-program behöver behörighet att komma åt nycklar eller hemligheter i valvet. Använd cmdleten [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för att bevilja behörighet till programmet, med hjälp av klient-ID: t (som genererades när programmet registrerades) som parametervärdet _– servicePrincipalName_ . Mer information finns i blogg inlägget [Azure Key Vault-Step by steg](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Ange nyckelvalvets åtkomstprincip för AD-program med PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a>Ange åtkomst princip för nyckel valvet för Azure AD-appen med Azure CLI
Använd [AZ-nyckel valv uppsättnings princip](/cli/azure/keyvault#az-keyvault-set-policy) för att ange åtkomst principen. Mer information finns i [hantera Key Vault med CLI 2,0](../../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Ge tjänstens huvudnamn som du skapade via Azure CLI-åtkomst att hämta hemligheter och radbyte nycklar med följande kommando:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a>Ange åtkomst princip för nyckel valvet för Azure AD-appen med portalen

1. Öppna resursgruppen med ditt nyckelvalv.
2. Välj ditt nyckel valv, gå till **åtkomst principer**och klicka sedan på **Lägg till ny**.
3. Under **Välj huvud konto**söker du efter det Azure AD-program som du skapade och väljer det. 
4. För **nyckel behörigheter**kontrollerar du **rad brytnings nyckeln** under **kryptografiska åtgärder**.
5. För **hemliga behörigheter**markerar du **Ange** under **hemliga hanterings åtgärder**.
6. Spara åtkomst principen genom att klicka på **OK** . 

![Azure Key Vault kryptografiska åtgärder - Wrap Key](./media/disk-encryption/keyvault-portal-fig3.png)

![Ange behörigheter för Azure Key Vault-hemlighet-](./media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a>Ange avancerade åtkomst principer för Key Vault
Azure-plattformen behöver åtkomst till krypteringsnycklar och hemligheter i ditt nyckelvalv och gör dem tillgängliga för den virtuella datorn för start och dekryptera volymerna. Aktivera diskkryptering på nyckelvalvet eller distributioner kommer att misslyckas.  

### <a name="bkmk_KVperPSH"></a>Ange avancerade åtkomst principer för nyckel valvet med Azure PowerShell
 Använd Key Vault PowerShell-cmdleten [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för att aktivera disk kryptering för nyckel valvet.

  - **Aktivera Key Vault för disk kryptering:** EnabledForDiskEncryption krävs för Azure Disk Encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Aktivera Key Vault för distribution vid behov:** Gör att Microsoft. Compute Resource-providern kan hämta hemligheter från det här nyckel valvet när det här nyckel valvet refereras till när en resurs skapas, till exempel när en virtuell dator skapas.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Aktivera Key Vault vid distribution av mallar, om det behövs:** Gör det möjligt för Azure Resource Manager att hämta hemligheter från det här nyckel valvet när det här nyckel valvet refereras till i en mall distribution.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a>Ange avancerade åtkomst principer för Key Vault med Azure CLI
Använd [AZ-uppdateringen](/cli/azure/keyvault#az-keyvault-update) för att aktivera disk kryptering för nyckel valvet. 

 - **Aktivera Key Vault för disk kryptering:** Enabled-for-Disk-Encryption krävs. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Aktivera Key Vault för distribution vid behov:** Tillåt Virtual Machines att hämta certifikat som lagras som hemligheter från valvet.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Aktivera Key Vault vid distribution av mallar, om det behövs:** Tillåt Resource Manager att hämta hemligheter från valvet.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a>Ange avancerade åtkomst principer för Key Vault via Azure Portal

1. Välj ditt nyckel valv, gå till **åtkomst principer**och **Klicka om du vill visa avancerade åtkomst principer**.
2. Markera rutan **ge till gång till Azure Disk Encryption för volym kryptering**.
3. Välj **Aktivera åtkomst till Azure Virtual Machines för distribution** och/eller **aktivera åtkomst till Azure Resource Manager för mall distribution**, om det behövs. 
4. Klicka på **Save** (Spara).

![Azure-nyckelvalv avancerade åtkomstprinciper](./media/disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a>Konfigurera en nyckel krypterings nyckel (valfritt)
Om du vill använda en krypteringsnyckel nyckel (KEK) för ett extra lager av säkerhet för krypteringsnycklar, lägger du till en KEK till ditt nyckelvalv. Använd cmdleten [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) för att skapa en nyckel krypterings nyckel i nyckel valvet. Du kan också importera en KEK från din lokala nyckelhantering HSM. Mer information finns i [Key Vault-dokumentationen](../../key-vault/key-vault-hsm-protected-keys.md). När du anger en nyckelkrypteringsnyckel använder Azure Disk Encryption nyckeln för att omsluta kryptering hemligheter innan du skriver till Key Vault. 

* Använd en typ av RSA-nyckel när du skapar nycklar. Azure Disk Encryption har ännu inte stöd för att använda Elliptic kurv nycklar.

* Din hemlighet i nyckelvalvet och KEK URL: er måste vara en ny version. Azure tillämpar den här begränsningen för versionshantering. Giltigt hemlighet och KEK URL: er finns i följande exempel:

  * Exempel på en giltig hemlig URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exempel på en giltig KEK-URL: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption stöder inte att ange portnummer som en del av key vault-hemligheter och KEK URL: er. Exempel på URL: er inte stöds och stöds key vault finns i följande exempel:

  * Oacceptabel URL för Key Vault *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Acceptabel nyckel valv-URL: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="bkmk_KEKPSH"></a>Konfigurera en nyckel krypterings nyckel med Azure PowerShell 
Innan du använder PowerShell-skriptet, bör du känna till kraven för Azure Disk Encryption att förstå steg i skriptet. Exempelskriptet behöva ändringar för din miljö. Det här skriptet skapar alla krav för Azure Disk Encryption och krypterar en befintlig IaaS VM, wrapping disk-krypteringsnyckeln med hjälp av en nyckel krypteringsnyckel. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a>Certifikatbaserad autentisering (valfritt)
Om du vill använda autentisering med certifikat, kan du ladda upp en till ditt nyckelvalv och distribuera den till klienten. Innan du använder PowerShell-skriptet, bör du känna till kraven för Azure Disk Encryption att förstå steg i skriptet. Exempelskriptet behöva ändringar för din miljö.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a>Certifikatbaserad autentisering och en KEK (valfritt)

Om du vill använda autentisering med certifikat och omsluter krypteringsnyckeln med en KEK, kan du använda den som en exempel-skriptet nedan. Innan du använder PowerShell-skriptet, bör du känna till alla tidigare Azure Disk Encryption-krav för att förstå steg i skriptet. Exempelskriptet behöva ändringar för din miljö.

> [!IMPORTANT]
> Azure AD-certifikatbaserad autentisering stöds för närvarande inte på virtuella Linux-datorer.



     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Nästa steg

[Aktivera Azure Disk Encryption med Azure AD på virtuella Linux-datorer (tidigare version)](disk-encryption-linux-aad.md)
