---
title: Förutsättningar – Azure Disk Encryption för virtuella IaaS-datorer | Microsoft Docs
description: Den här artikeln innehåller förutsättningar för att använda Microsoft Azure Disk Encryption för virtuella IaaS-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 09/30/2019
ms.custom: seodec18
ms.openlocfilehash: 551918373f8292d798980600d6e0d43add55bd18
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828276"
---
# <a name="azure-disk-encryption-prerequisites"></a>Krav för Azure Disk Encryption

Den här artikeln krävs för Azure Disk Encryption, förklarar objekt som måste vara uppfyllda innan du kan använda Azure Disk Encryption. Azure Disk Encryption är integrerad med [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) för att hantera krypteringsnycklar. Du kan använda [Azure PowerShell](/powershell/azure/overview), [Azure CLI](/cli/azure/), eller [Azure-portalen](https://portal.azure.com) att konfigurera Azure Disk Encryption.

Innan du aktiverar Azure Disk Encryption på virtuella Azure IaaS-datorer för de scenarier som stöds som beskrivs i den [översikt över Azure Disk Encryption](azure-security-disk-encryption-overview.md) artikel, se till att ha kraven på plats. 

> [!WARNING]
> - Om du tidigare har använt [Azure Disk Encryption med Azure AD-appen](azure-security-disk-encryption-prerequisites-aad.md) för att kryptera den här virtuella datorn måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Du kan inte använda [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) på den här krypterade virtuella datorn eftersom detta inte stöds, vilket innebär att det inte finns stöd för att växla bort från AAD-program för den här krypterade virtuella datorn än.
> - Vissa rekommendationerna kan öka data, nätverk eller Resursanvändning för beräkning, vilket resulterar i ytterligare kostnader för licens eller prenumeration. Du måste ha en giltig aktiv Azure-prenumeration att skapa resurser i Azure i regionerna som stöds.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-vm-sizes"></a>VM-storlekar som stöds

Azure Disk Encryption är inte tillgänglig på [virtuella datorer i A-serien](https://azure.microsoft.com/pricing/details/virtual-machines/series/). Azure Disk Encryption är tillgängligt på andra virtuella datorer som uppfyller de minsta minnes kraven:

| Virtuell dator | Minsta minnes krav |
|--|--|
| Virtuella Windows-datorer | 2 GB |
| Virtuella Linux-datorer vid endast kryptering av data volymer| 2 GB |
| Virtuella Linux-datorer vid kryptering av både data-och OS-volymer och där roten (/) fil systemets användning är 4 GB eller mindre | 8 GB |
| Virtuella Linux-datorer vid kryptering av både data-och OS-volymer och där roten (/) fil systemets användning är större än 4 GB | Rot fil systemets användning * 2. Till exempel kräver en 16 GB rot fil system användning minst 32 GB RAM-minne |

När operativ systemets disk krypterings process har slutförts på virtuella Linux-datorer kan den virtuella datorn konfigureras för att köras med mindre minne. 

> [!NOTE]
> Det finns inte tillräckligt med disk kryptering för Linux-operativsystem för [Virtual Machine Scale Sets](../virtual-machine-scale-sets/index.yml).

Azure Disk Encryption är också tillgängligt för virtuella datorer med Premium Storage. 

## <a name="supported-operating-systems"></a>Operativsystem som stöds

### <a name="windows"></a>Windows

- Windows-klient: Windows 8 och senare.
- Windows Server: Windows Server 2008 R2 och senare.  
 
> [!NOTE]
> Windows Server 2008 R2 kräver att .NET Framework 4,5 installeras för kryptering. installera den från Windows Update med den valfria uppdateringen Microsoft .NET Framework 4.5.2 för Windows Server 2008 R2 x64-baserade system ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 Core och Windows Server 2016 Core kräver att BdeHdCfg-komponenten installeras på den virtuella datorn för kryptering.


### <a name="linux"></a>Linux 

Azure Disk Encryption stöds på en delmängd av [Azure-godkända Linux-distributioner](../virtual-machines/linux/endorsed-distros.md), som i sig är en delmängd av alla möjliga distributioner av Linux-servrar.

![Venndiagram av Linux Server-distributioner som stöder Azure Disk Encryption](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Linux Server-distributioner som inte har godkänts av Azure stöder inte Azure Disk Encryption och, av de som har påtecknats, endast följande distributioner och versioner Azure Disk Encryption:

| Linux-distribution | Version | Volymtyp som stöds för kryptering|
| --- | --- |--- |
| Ubuntu | 18,04| OS- och disk |
| Ubuntu | 16.04| OS- och disk |
| Ubuntu | 14.04.5</br>[med Azures justerade kernel uppdaterat till 4,15 eller senare](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | OS- och disk |
| RHEL | 7,7 | Operativ system och data disk (se OBS! nedan) |
| RHEL | 7,6 | Operativ system och data disk (se OBS! nedan) |
| RHEL | 7.5 | Operativ system och data disk (se OBS! nedan) |
| RHEL | 7.4 | Operativ system och data disk (se OBS! nedan) |
| RHEL | 7.3 | Operativ system och data disk (se OBS! nedan) |
| RHEL | 7.2 | Operativ system och data disk (se OBS! nedan) |
| RHEL | 6.8 | Data disk (se OBS! nedan) |
| RHEL | 6.7 | Data disk (se OBS! nedan) |
| CentOS | 7,7 | OS- och disk |
| CentOS | 7,6 | OS- och disk |
| CentOS | 7.5 | OS- och disk |
| CentOS | 7.4 | OS- och disk |
| CentOS | 7.3 | OS- och disk |
| CentOS | 7.2n | OS- och disk |
| CentOS | 6.8 | Datadisk |
| openSUSE | 42,3 | Datadisk |
| SLES | 12-SP4 | Datadisk |
| SLES | 12-SP3 | Datadisk |

> [!NOTE]
> Den nya ADE-implementeringen stöds för RHEL OS och datadisk för RHEL7 avbildningar enligt principen betala per användning. ADE stöds för närvarande inte för RHEL-avbildningar (BYOS). Mer information finns i [Azure Disk Encryption för Linux](azure-security-disk-encryption-linux.md) .

- Azure Disk Encryption kräver att dina nyckelvalv och virtuella datorer finns i samma Azure-region och prenumeration. Konfigurera resurserna i olika områden orsakar ett fel i Azure Disk Encryption-funktionen aktiveras.

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>Ytterligare krav för virtuella Linux IaaS-datorer 

- Azure Disk Encryption kräver att dm-crypt-och vfat-modulerna finns i systemet. Om du tar bort eller inaktiverar vfat från standard avbildningen så förhindras systemet från att läsa nyckel volymen och hämta den nyckel som behövs för att låsa upp diskarna vid efterföljande omstarter. System härdnings steg som tar bort vfat-modulen från systemet är inte kompatibla med Azure Disk Encryption. 
- Innan du aktiverar kryptering måste på diskar som ska krypteras anges korrekt på/etc/fstab. Använd ett beständigt block enhetsnamn för den här posten som enhetens namn i formatet ”/ dev/sdX” det går inte att förlita sig på som ska associeras med samma disk mellan omstarter, särskilt när kryptering används. Mer information om det här problemet finns i: [Felsöka ändringar av enhets namn för virtuella Linux-datorer](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Kontrollera att/etc/fstab-inställningarna har konfigurerats korrekt för montering. Kör mount - ett kommando för att konfigurera de här inställningarna, eller starta om den virtuella datorn och utlösa återmontering på så sätt. När detta är slutfört kan du kontrollera resultatet av kommandot lsblk att verifiera att enheten fortfarande är ansluten. 
  - Om filen/etc/fstab inte montera enheten korrekt innan du aktiverar kryptering, Azure Disk Encryption inte montera den korrekt.
  - Azure Disk Encryption-processen flyttar mount-information från/etc/fstab och i sin egen konfigurationsfilen som en del av krypteringsprocessen. Inte vara alarmed att se posten saknas i/etc/fstab när data diskkryptering har slutförts.
  - Innan du startar kryptering måste du stoppa alla tjänster och processer som kan skrivas till monterade data diskar och inaktivera dem, så att de inte startar om automatiskt efter en omstart. Dessa kan hålla filerna öppna på dessa partitioner, vilket förhindrar krypterings proceduren att ommontera dem, vilket orsakar att krypteringen Miss lyckas. 
  - Efter omstart tar det tid för Azure Disk Encryption-processen för att montera de nyligen krypterade diskarna. De kommer inte blir tillgängliga omedelbart efter en omstart. Processen behöver tid att börja låsa upp och sedan montera de krypterade enheterna innan är tillgängliga för andra processer att få åtkomst till. Den här processen kan ta mer än en minut efter omstart beroende på system-egenskaper.

Ett exempel på kommandon som kan användas för att montera datadiskarna och skapa de nödvändiga/etc/fstab poster finns i [linjer 244-248 i den här skriptfilen](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 

## <a name="bkmk_GPO"></a> Nätverk och en Grupprincip

**Om du vill aktivera Azure Disk Encryption måste funktion, den virtuella IaaS-datorer uppfylla följande nätverk endpoint konfigurationskrav:**
  - Om du vill hämta en token för att ansluta till ditt nyckelvalv, IaaS VM måste vara kan ansluta till en Azure Active Directory-slutpunkt \[login.microsoftonline.com\].
  - Om du vill skriva dem till ditt nyckelvalv kunna IaaS VM ansluta till slutpunkten för nyckelvalvet.
  - IaaS VM måste kunna ansluta till en Azure storage-slutpunkt som är värd för lagringsplatsen Azure-tillägget och ett Azure storage-konto som är värd för VHD-filer.
  -  Om din säkerhetsprincip begränsar åtkomst från virtuella Azure-datorer till Internet, kan du matcha den föregående URI: N och konfigurera en specifik regel som tillåter utgående anslutning till IP-adresserna. Mer information finns i [Azure Key Vault bakom en brandvägg](../key-vault/key-vault-access-behind-firewall.md).    


**Grupprincip:**
 - Azure Disk Encryption-lösningen använder de externa nyckelskyddet för BitLocker för Windows virtuella IaaS-datorer. Domänanslutna virtuella datorer, inte skicka någon grupprinciper som tillämpar TPM-skydd. Läs om hur en grupprincip för ”Tillåt BitLocker utan en kompatibel TPM” [gruppolicy referens för BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  BitLocker-principen på domänanslutna virtuella datorer med anpassad grup princip måste innehålla följande inställning: [Konfigurera användar lagring av BitLocker-återställningsinformation – > tillåt 256-bitars återställnings nyckel](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption Miss fungerar när anpassade grup princip inställningar för BitLocker inte är kompatibla. Tillämpa den nya principen på datorer som inte har rätt principinställningen tvinga den nya principen för att uppdatera (gpupdate.exe/Force) och sedan omstart kan krävas.

- Azure Disk Encryption Miss fungerar om grup principen på domän nivå blockerar AES-CBC-algoritmen som används av BitLocker.


## <a name="bkmk_PSH"></a> Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) tillhandahåller en uppsättning cmdletar som använder den [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) modellen för att hantera dina Azure-resurser. Du kan använda den i din webbläsare med [Azure Cloud Shell](../cloud-shell/overview.md), eller så kan du installera den på den lokala datorn med hjälp av anvisningarna nedan för att använda den i PowerShell-sessioner. Om du redan har installerat lokalt kan du kontrollera att du använder den senaste versionen av Azure PowerShell SDK-version för att konfigurera Azure Disk Encryption. Ladda ned den senaste versionen av [Azure PowerShell version](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Installera Azure PowerShell för användning på den lokala datorn (valfritt): 
1. Följ anvisningarna i länkarna för ditt operativsystem sedan fortsätta men resten av stegen nedan.      
   - [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps). 
     - Installera PowerShellGet, Azure PowerShell och läsa in AZ-modulen. 

2. Verifiera de installerade versionerna av AZ-modulen. Om det behövs [uppdatera Azure PowerShell-modulen](/powershell/azure/install-az-ps#update-the-azure-powershell-module).
    Vi rekommenderar att du använder den senaste versionen av AZ-modulen.

     ```powershell
     Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. Logga in på Azure med cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
     
     ```azurepowershell-interactive
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

4.  Om det behövs kan du granska [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="bkmk_CLI"></a> Installera Azure CLI för användning på den lokala datorn (valfritt)

Den [Azure CLI 2.0](/cli/azure) är ett kommandoradsverktyg för att hantera Azure-resurser. CLI är utformat för att fråga efter data, stöd för långvariga åtgärder som icke-blockerande processer och göra skript enkelt flexibelt. Du kan använda det i webbläsaren med [Azure Cloud Shell](../cloud-shell/overview.md) eller installera det på din lokala dator och använda det i PowerShell-sessioner.

1. [Installera Azure CLI](/cli/azure/install-azure-cli) för användning på den lokala datorn (valfritt):

2. Kontrollera den installerade versionen.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Logga in på Azure med [az-inloggning](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Granska [Kom igång med Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) om det behövs. 


## <a name="prerequisite-workflow-for-key-vault"></a>Nödvändiga arbetsflöde för Key Vault
Om du redan är bekant med Key Vault och Azure AD-krav för Azure Disk Encryption kan du använda den [PowerShell-skript för Azure Disk Encryption krav](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Mer information om hur du använder skriptet krav finns i den [kryptera en virtuell dator-Snabbstart](../virtual-machines/linux/disk-encryption-powershell-quickstart.md) och [Azure Disk Encryption bilaga](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Om det behövs skapar du en resursgrupp.
2. Skapa ett nyckelvalv. 
3. Ange nyckelvalv avancerade åtkomstprinciper.

>[!WARNING]
>Se till att du inte krypterar alla befintliga virtuella datorer med den innan du tar bort ett nyckelvalv. Att skydda ett valv mot oavsiktlig borttagning [aktivera mjuk borttagning](../key-vault/key-vault-soft-delete-powershell.md#enabling-soft-delete) och en [resurslås](../azure-resource-manager/resource-group-lock-resources.md) på valvet. 
 
## <a name="bkmk_KeyVault"></a> Skapa ett nyckelvalv 
Azure Disk Encryption är integrerad med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) för att styra och hantera diskkrypteringsnycklar och hemligheter i key vault-prenumeration. Du kan skapa ett nyckelvalv eller Använd en befintlig för Azure Disk Encryption. Läs mer om nyckelvalv [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md) och [säkra ditt nyckelvalv](../key-vault/key-vault-secure-your-key-vault.md). Du kan använda en Resource Manager-mall, Azure PowerShell eller Azure CLI för att skapa ett nyckelvalv. 


>[!WARNING]
>För att se till att kryptering hemligheterna inte går över regionala gränser, måste Azure Disk Encryption Key Vault och de virtuella datorerna för att finnas i samma region. Skapa och använda ett Nyckelvalv som är i samma region som den virtuella datorn måste vara krypterade. 


### <a name="bkmk_KVPSH"></a> Skapa ett nyckelvalv med PowerShell

Du kan skapa ett nyckel valv med Azure PowerShell med hjälp av cmdleten [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Ytterligare cmdlets för Key Vault finns i [AZ. nyckel valv](/powershell/module/az.keyvault/). 

1. Om det behövs [ansluta till din Azure-prenumeration](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Skapa en ny resurs grupp, om det behövs, med [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Om du vill visa en lista över data Center platser använder du [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Skapa ett nytt nyckel valv med [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Obs den **valv namn**, **resursgruppnamn**, **resurs-ID**, **Valvets URI**, och **objekt-ID** som returneras för senare användning när du krypterar diskarna. 


### <a name="bkmk_KVCLI"></a> Skapa ett nyckelvalv med Azure CLI
Du kan hantera ditt nyckelvalv med Azure CLI med hjälp av den [az keyvault](/cli/azure/keyvault#commands) kommandon. Du kan skapa ett nyckelvalv med [az keyvault skapa](/cli/azure/keyvault#az-keyvault-create).

1. Om det behövs [ansluta till din Azure-prenumeration](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Skapa en ny resursgrupp, om det behövs, med [az gruppen skapa](/cli/azure/group#az-group-create). Använd om du vill visa en lista över platser [az konto list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Skapa ett nytt nyckelvalv med [az keyvault skapa](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Obs den **Valvnamnet** (namn), **Resursgruppsnamn**, **resurs-ID** (ID), **Valvets URI**, och **objekt-ID** som returneras för användning senare. 

### <a name="bkmk_KVRM"></a> Skapa ett nyckelvalv med en Resource Manager-mall

Du kan skapa ett nyckelvalv med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Klicka på Azure snabbstarts-mall **distribuera till Azure**.
2. Välj den prenumeration, resursgrupp, plats för resursgruppen, Key Vault namn, objekt-ID, juridiska villkor och avtal och klickar sedan på **köp**. 


## <a name="bkmk_KVper"></a> Ställ in avancerade åtkomstprinciper för nyckelvalvet
Azure-plattformen behöver åtkomst till krypteringsnycklar och hemligheter i ditt nyckelvalv och gör dem tillgängliga för den virtuella datorn för start och dekryptera volymerna. Aktivera diskkryptering på nyckelvalvet eller distributioner kommer att misslyckas.  

### <a name="bkmk_KVperPSH"></a> Ställ in avancerade åtkomstprinciper med Azure PowerShell för nyckelvalvet
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

### <a name="bkmk_KVperCLI"></a> Ställ in avancerade åtkomstprinciper med hjälp av Azure CLI för nyckelvalvet
Använd [az keyvault update](/cli/azure/keyvault#az-keyvault-update) vill aktivera diskkryptering för key vault. 

 - **Aktivera Key Vault för disk kryptering:** Enabled-for-Disk-Encryption krävs. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Aktivera Key Vault för distribution vid behov:** Gör att Microsoft. Compute Resource-providern kan hämta hemligheter från det här nyckel valvet när det här nyckel valvet refereras till när en resurs skapas, till exempel när en virtuell dator skapas.

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Aktivera Key Vault vid distribution av mallar, om det behövs:** Tillåt Resource Manager att hämta hemligheter från valvet.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Ställ in avancerade åtkomstprinciper via Azure portal för nyckelvalvet

1. Välj din keyvault, gå till **åtkomstprinciper**, och **Klicka om du vill visa avancerade åtkomstprinciper**.
2. Markera rutan **ge åtkomst till Azure Disk Encryption för volymkryptering**.
3. Välj **Aktivera åtkomst till Azure Virtual Machines för distribution av** och/eller **Aktivera åtkomst till Azure Resource Manager för malldistribution**, om det behövs. 
4. Klicka på **Spara**.

    ![Azure-nyckelvalv avancerade åtkomstprinciper](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Konfigurera en nyckelkrypteringsnyckel (valfritt)
Om du vill använda en krypteringsnyckel nyckel (KEK) för ett extra lager av säkerhet för krypteringsnycklar, lägger du till en KEK till ditt nyckelvalv. Använd cmdleten [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) för att skapa en nyckel krypterings nyckel i nyckel valvet. Du kan också importera en KEK från din lokala nyckelhantering HSM. Mer information finns i [dokumentation om Key Vault](../key-vault/key-vault-hsm-protected-keys.md). När du anger en nyckelkrypteringsnyckel använder Azure Disk Encryption nyckeln för att omsluta kryptering hemligheter innan du skriver till Key Vault.

* Använd en typ av RSA-nyckel när du skapar nycklar. Azure Disk Encryption har ännu inte stöd för att använda Elliptic kurv nycklar.

* Din hemlighet i nyckelvalvet och KEK URL: er måste vara en ny version. Azure tillämpar den här begränsningen för versionshantering. Giltigt hemlighet och KEK URL: er finns i följande exempel:

  * Exempel på en giltig hemlig URL:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exempel på en giltig KEK-URL:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption stöder inte att ange portnummer som en del av key vault-hemligheter och KEK URL: er. Exempel på URL: er inte stöds och stöds key vault finns i följande exempel:

  * Oacceptabel key vault-Webbadress  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Godkända key vault-Webbadress:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*


### <a name="bkmk_KEKPSH"></a> Konfigurera en nyckelkrypteringsnyckel med Azure PowerShell 
Innan du använder PowerShell-skriptet, bör du känna till kraven för Azure Disk Encryption att förstå steg i skriptet. Exempelskriptet behöva ändringar för din miljö. Det här skriptet skapar alla krav för Azure Disk Encryption och krypterar en befintlig IaaS VM, wrapping disk-krypteringsnyckeln med hjälp av en nyckel krypteringsnyckel. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).VaultUri;
     
 #Step 2: Enable the vault for disk encryption.
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
      
 #Step 3: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'HSM';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 4: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```


 
## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Aktivera Azure Disk Encryption för Windows](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Aktivera Azure Disk Encryption för Linux](azure-security-disk-encryption-linux.md)

