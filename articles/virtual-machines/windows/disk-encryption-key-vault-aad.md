---
title: Skapa och konfigurera ett nyckelvalv för Azure Disk-kryptering med Azure AD (tidigare version)
description: Den här artikeln innehåller förutsättningar för att använda Microsoft Azure Disk Encryption för virtuella IaaS-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 5a42b1e0dc82b3340bbebe176c71cb1754d00664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243385"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Skapa och konfigurera ett nyckelvalv för Azure Disk-kryptering med Azure AD (tidigare version)

**Den nya versionen av Azure Disk Encryption eliminerar kravet på att tillhandahålla en Azure AD-programparameter för att aktivera VM-diskkryptering. Med den nya versionen behöver du inte längre ange Azure AD-autentiseringsuppgifter under aktivera krypteringssteget. Alla nya virtuella datorer måste krypteras utan Azure AD-programparametrar med den nya versionen. Information om hur du visar instruktioner för att aktivera VM-diskkryptering med den nya versionen finns i [Azure Disk Encryption](disk-encryption-overview.md). Virtuella datorer som redan har krypterats med Azure AD-programparametrar stöds fortfarande och bör fortsätta att underhållas med AAD-syntaxen.**

Azure Disk Encryption använder Azure Key Vault för att styra och hantera diskkrypteringsnycklar och hemligheter.  Mer information om nyckelvalv finns i [Komma igång med Azure Key Vault](../../key-vault/key-vault-get-started.md) och Säkra ditt [nyckelvalv](../../key-vault/key-vault-secure-your-key-vault.md). 

Att skapa och konfigurera ett nyckelvalv för användning med Azure Disk Encryption med Azure AD (tidigare version) innebär tre steg:

1. Skapa ett nyckelvalv. 
2. Konfigurera ett Azure AD-program och tjänsthuvudnamn.
3. Ange nyckelvalvets åtkomstprincip för Azure AD-appen
4. Ställa in avancerade åtkomstprinciper för nyckelvalvet
 
Du kan också, om du vill, generera eller importera en nyckelkrypteringsnyckel (KEK).

Se [huvudartikeln Skapa och konfigurera ett nyckelvalv för Azure DiskKryptering](disk-encryption-key-vault.md) för steg för steg om hur [du installerar verktyg och ansluter till Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure).

> [!Note]
> Stegen i den här artikeln är automatiserade i [Azure Disk Encryption förutsättningar CLI-skript](https://github.com/ejarvi/ade-cli-getting-started) och Azure Disk Kryptering förutsättningar [PowerShell skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Skapa ett nyckelvalv 
Azure Disk Encryption är integrerat med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att hjälpa dig att styra och hantera diskkrypteringsnycklar och hemligheter i din nyckelvalvsprenumeration. Du kan skapa ett nyckelvalv eller använda ett befintligt för Azure Disk Encryption. Mer information om nyckelvalv finns i [Komma igång med Azure Key Vault](../../key-vault/key-vault-get-started.md) och Säkra ditt [nyckelvalv](../../key-vault/key-vault-secure-your-key-vault.md). Du kan använda en Resource Manager-mall, Azure PowerShell eller Azure CLI för att skapa ett nyckelvalv. 


>[!WARNING]
>För att se till att krypteringshemligheterna inte korsar regionala gränser behöver Azure Disk Encryption nyckelvalvet och de virtuella datorerna som ska samlokalgöras i samma region. Skapa och använd ett nyckelvalv som finns i samma region som den virtuella datorn som ska krypteras. 


### <a name="create-a-key-vault-with-powershell"></a>Skapa ett nyckelvalv med PowerShell

Du kan skapa ett nyckelvalv med Azure PowerShell med cmdleten [New-AzKeyVault.](/powershell/module/az.keyvault/New-azKeyVault) Ytterligare cmdlets för Key Vault finns i [Az.KeyVault](/powershell/module/az.keyvault/). 

1. Skapa en ny resursgrupp, om det behövs, med [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Om du vill visa datacenterplatser använder du [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Skapa ett nytt nyckelvalv med [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Observera **arkivnamn,** **resursgruppnamn,** **resurs-ID,** **Arkiv-URI**och **objekt-ID** som returneras för senare användning när du krypterar diskarna. 


### <a name="create-a-key-vault-with-azure-cli"></a>Skapa ett nyckelvalv med Azure CLI
Du kan hantera ditt nyckelvalv med Azure CLI med az [keyvault-kommandona.](/cli/azure/keyvault#commands) Om du vill skapa ett nyckelvalv använder du [az keyvault create](/cli/azure/keyvault#az-keyvault-create).

1. Skapa en ny resursgrupp om det behövs med [az-gruppskapa](/cli/azure/group#az-group-create). Om du vill visa platser använder du [plats för az-kontolista](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Skapa ett nytt nyckelvalv med [az keyvault create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Observera **arkivnamnet** (namn), **resursgruppnamn,** **resurs-ID** (ID), **Arkiv-URI**och **det objekt-ID** som returneras för användning senare. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Skapa ett nyckelvalv med en Resource Manager-mall

Du kan skapa ett nyckelvalv med hjälp av [resource manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Klicka på Distribuera till **Azure**i snabbstartsmallen i Azure .
2. Välj prenumeration, resursgrupp, resursgruppsplats, Key Vault-namn, Objekt-ID, juridiska termer och avtal och klicka sedan på **Inköp**. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Konfigurera ett huvudnamn för Azure AD-appen och tjänsten 
När du behöver kryptering som ska aktiveras på en virtuell dator som körs i Azure genererar och skriver Azure Disk Encryption krypteringsnycklarna till ditt nyckelvalv. För att hantera krypteringsnycklar i nyckelvalvet krävs Azure AD-autentisering. Skapa ett Azure AD-program för detta ändamål. I autentiseringssyfte kan du använda antingen klienthemlig autentisering eller [klientcertifikatbaserad Azure AD-autentisering](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Konfigurera en Azure AD-app och tjänsthuvudnamn med Azure PowerShell 
Om du vill köra följande kommandon hämtar och använder du [Azure AD PowerShell-modulen](/powershell/azure/active-directory/install-adv2). 

1. Använd [PowerShell-cmdleten New-AzADApplication](/powershell/module/az.resources/new-azadapplication) för att skapa ett Azure AD-program. MyApplicationHomePage och MyApplicationUri kan vara alla värden du vill.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $azureAdApplication.ApplicationId är Azure AD ClientID och $aadClientSecret är klienthemligheten som du kommer att använda senare för att aktivera Azure Disk Encryption. Skydda Azure AD-klientens hemlighet på lämpligt sätt. Om `$azureAdApplication.ApplicationId` du kör visas ApplicationID.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Konfigurera en Azure AD-app och tjänsthuvudnamn med Azure CLI

Du kan hantera dina tjänsthuvudnamn med Azure CLI med az ad sp-kommandona. [az ad sp](/cli/azure/ad/sp) Mer information finns i [Skapa ett huvudnamn för Azure-tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Skapa ett nytt huvudnamn för tjänsten.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Den returnerade appId är Azure AD ClientID som används i andra kommandon. Det är också SPN du använder för az keyvault set-policy. Lösenordet är klienthemligheten som du bör använda senare för att aktivera Azure Disk Encryption. Skydda Azure AD-klientens hemlighet på lämpligt sätt.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Konfigurera en Azure AD-app och tjänsthuvudnamn via Azure-portalen
Använd stegen från [användningsportalen för att skapa ett Azure Active Directory-program och tjänsthuvudnamn som kan komma åt resursartikel](../../active-directory/develop/howto-create-service-principal-portal.md) för att skapa ett Azure AD-program. Varje steg som anges nedan tar dig direkt till artikeln avsnittet för att slutföra. 

1. [Verifiera nödvändiga behörigheter](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Skapa ett Azure Active Directory-program](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Du kan använda vilket namn och inloggnings-URL du vill när du skapar programmet.
3. [Hämta program-ID:t och autentiseringsnyckeln](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - Autentiseringsnyckeln är klienthemligheten och används som AadClientSecret för Set-AzVMDiskEncryptionExtension. 
        - Autentiseringsnyckeln används av programmet som en autentiseringsperson för att logga in på Azure AD. I Azure-portalen kallas den här hemligheten nycklar, men har ingen relation till nyckelvalv. Säkra den här hemligheten på lämpligt sätt. 
     - Program-ID kommer att användas senare som AadClientId för Set-AzVMDiskEncryptionExtension och som ServicePrincipalName för Set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Ange nyckelvalvets åtkomstprincip för Azure AD-appen
Azure Diskkryptering behöver klient-ID och klienthemligheten för Azure Active Directory-programmet som har behörighet att skriva hemligheter till Nyckelvalvet för att skriva krypteringshemligheter till Key Vault. 

> [!NOTE]
> Azure Disk Encryption kräver att du konfigurerar följande åtkomstprinciper till ditt Azure AD-klientprogram: _WrapKey_ och _Ange_ behörigheter.

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Ange principen för nyckelvalvsåtkomst för Azure AD-appen med Azure PowerShell
Ditt Azure AD-program behöver rättigheter för att komma åt nycklar eller hemligheter i valvet. Använd [cmdleten Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för att bevilja behörigheter till programmet med klient-ID (som genererades när programmet registrerades) som parametervärdet _–ServicePrincipalName._ Mer information finns i blogginlägget [Azure Key Vault - Steg för steg](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Ange åtkomstprincipen för nyckelvalvet för AD-programmet med PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Ange princip för nyckelvalvsåtkomst för Azure AD-appen med Azure CLI
Använd [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) för att ange åtkomstprincipen. Mer information finns i [Hantera nyckelvalv med CLI 2.0](../../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Ge tjänstens huvudnamn som du skapade via Azure CLI-åtkomst för att hämta hemligheter och radbryt nycklar med följande kommando:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Ange principen för nyckelvalvsåtkomst för Azure AD-appen med portalen

1. Öppna resursgruppen med nyckelvalvet.
2. Markera nyckelvalvet, gå till **Åtkomstprinciper**och klicka sedan på **Lägg till nya**.
3. Under **Välj huvudnamn**söker du efter Azure AD-programmet som du skapade och väljer det. 
4. Om **du vill ha nyckelbehörigheter**markerar du **Radbrytnyckel** under **Kryptografiska operationer**.
5. **Hemliga behörigheter**finns i **Ange** under **Hemliga hanteringsåtgärder**.
6. Spara åtkomstprincipen genom att klicka på **OK.** 

![Krypteringsåtgärder för Azure Key Vault - radbrytningsnyckel](../media/disk-encryption/keyvault-portal-fig3.png)

![Hemliga behörigheter för Azure Key Vault - Ange](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Ställa in avancerade åtkomstprinciper för nyckelvalvet
Azure-plattformen behöver åtkomst till krypteringsnycklarna eller hemligheterna i nyckelvalvet för att göra dem tillgängliga för den virtuella datorn för att starta och dekryptera volymerna. Aktivera diskkryptering på nyckelvalvet eller distributioner misslyckas.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Ange avancerade principer för nyckelvalv med Azure PowerShell
 Använd nyckelvalvet PowerShell cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) för att aktivera diskkryptering för nyckelvalvet.

  - **Aktivera Key Vault för diskkryptering:** EnabledForDiskEncryption krävs för Azure Disk-kryptering.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Aktivera Key Vault för distribution om det behövs:** Gör det möjligt för Microsoft.Compute-resursprovidern att hämta hemligheter från det här nyckelvalvet när det här nyckelvalvet refereras till när du skapar en virtuell dator.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Aktivera Key Vault för malldistribution om det behövs:** Gör det möjligt för Azure Resource Manager att hämta hemligheter från det här nyckelvalvet när det här nyckelvalvet refereras i en malldistribution.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Ange avancerade principer för nyckelvalv med hjälp av Azure CLI
Använd [az keyvault-uppdatering](/cli/azure/keyvault#az-keyvault-update) för att aktivera diskkryptering för nyckelvalvet. 

 - **Aktivera Key Vault för diskkryptering:** Aktiverad-för-disk-kryptering krävs. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Aktivera Key Vault för distribution om det behövs:** Tillåt virtuella datorer att hämta certifikat som lagras som hemligheter från valvet.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Aktivera Key Vault för malldistribution om det behövs:** Tillåt att Resurshanteraren hämtar hemligheter från valvet.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Ange avancerade principer för nyckelvalv via Azure-portalen

1. Välj din keyvault, gå till **Åtkomstprinciper**och **Klicka för att visa avancerade åtkomstprinciper**.
2. Välj rutan **Aktivera åtkomst till Azure Disk Encryption för volymkryptering**.
3. Välj **Aktivera åtkomst till virtuella Azure-datorer för distribution** och/eller Aktivera åtkomst till Azure Resource Manager för **malldistribution**, om det behövs. 
4. Klicka på **Spara**.

![Avancerade åtkomstprinciper för Azure key vault](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Konfigurera en nyckelkrypteringsnyckel (valfritt)
Om du vill använda en nyckelkrypteringsnyckel (KEK) för ett extra säkerhetslager för krypteringsnycklar lägger du till en KEK i nyckelvalvet. Använd cmdleten [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) för att skapa en nyckelkrypteringsnyckel i nyckelvalvet. Du kan också importera en KEK från din lokala nyckelhantering HSM. Mer information finns i Dokumentation för [Nyckelvalv](../../key-vault/key-vault-hsm-protected-keys.md). När en nyckelkrypteringsnyckel anges använder Azure Disk Encryption den nyckeln för att radbrytas krypteringshemligheterna innan du skriver till Key Vault. 

* När du skapar nycklar använder du en RSA-nyckeltyp. Azure Disk Encryption stöder ännu inte elliptic curve-nycklar.

* Dina nyckelvalvshemligheter och KEK-url:er måste vara versionsinrerade. Azure tillämpar den här begränsningen av versionshantering. För giltiga hemliga och KEK-url:er finns i följande exempel:

  * Exempel på en giltig hemlig URL:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exempel på en giltig KEK-URL:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption stöder inte att ange portnummer som en del av nyckelvalvshemligheter och KEK-url:er. Exempel på url:er som inte stöds och stöds av nyckelvalv finns i följande exempel:

  * URL för nyckelvalvet är oacceptabelt*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Url för godtagbart nyckelvalv:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Konfigurera en nyckelkrypteringsnyckel med Azure PowerShell 
Innan du använder PowerShell-skriptet bör du känna till azure diskkrypteringsförutsättningsförutsättningarna för att förstå stegen i skriptet. Exempelskriptet kan behöva ändras för din miljö. Det här skriptet skapar alla förutsättningar för Azure Disk Encryption och krypterar en befintlig IaaS-VM och slår in diskkrypteringsnyckeln med hjälp av en nyckelkrypteringsnyckel. 

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

## <a name="certificate-based-authentication-optional"></a>Certifikatbaserad autentisering (valfritt)
Om du vill använda certifikatautentisering kan du ladda upp en till nyckelvalvet och distribuera det till klienten. Innan du använder PowerShell-skriptet bör du känna till azure diskkrypteringsförutsättningsförutsättningarna för att förstå stegen i skriptet. Exempelskriptet kan behöva ändras för din miljö.

     
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

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Certifikatbaserad autentisering och en KEK (valfritt)

Om du vill använda certifikatautentisering och slå in krypteringsnyckeln med en KEK kan du använda skriptet nedan som exempel. Innan du använder PowerShell-skriptet bör du känna till alla tidigare azure diskkrypteringsförutnämningar för att förstå stegen i skriptet. Exempelskriptet kan behöva ändras för din miljö.

     
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

[Aktivera Azure Disk Encryption med Azure AD på virtuella Windows-datorer (tidigare version)](disk-encryption-windows-aad.md)
