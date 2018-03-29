## <a name="prepare-for-akv-integration"></a>Förbereda för AKV-integreringen
Om du vill använda Azure Key Vault-integrering för att konfigurera SQL Server-VM, finns det flera förutsättningar: 

1. [Installera Azure Powershell](#install-azure-powershell)
2. [Skapa en Azure Active Directory](#create-an-azure-active-directory)
3. [Skapa ett nyckelvalv](#create-a-key-vault)

I följande avsnitt beskrivs dessa krav och den information du behöver samla in för att senare Kör PowerShell-cmdlets.

### <a name="install-azure-powershell"></a>Installera Azure PowerShell
Kontrollera att du har installerat den senaste Azure PowerShell SDK. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="register-an-application-in-your-azure-active-directory"></a>Registrera ett program i Azure Active Directory
Måste du ha en [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) i din prenumeration. Bland många fördelar kan du ge behörighet till nyckelvalvet för vissa användare och program.

Registrera ett program med AAD. Detta ger ett huvudnamn för tjänsten-konto som har åtkomst till nyckelvalvet som behöver den virtuella datorn. Azure Key Vault-artikeln hittar de här stegen i den [registrera ett program med Azure Active Directory](../articles/key-vault/key-vault-get-started.md#register) avsnitt eller du kan se steg med skärmdumpar i den **hämta en identitet för programavsnittet**  av [i det här blogginlägget](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Observera att du behöver samla in följande information under registreringen som behövs för senare när du aktiverar Azure Key Vault-integrering på din SQL-VM innan du utför de här stegen.

* När programmet har lagts till, finns det **program-ID** på den **registrerad app** bladet.   
    Program-ID tilldelas senare till den **$spName** (Service Principal name)-parametern i PowerShell-skript för att aktivera Azure Key Vault-integrering. 
* Under de här stegen när du skapar din nyckel för att kopiera hemligheten för din nyckel som visas i följande skärmbild. Den här nyckeln hemlig tilldelas senare till den **$spSecret** (Service Principal hemliga) parameter i PowerShell-skript.  

* Program-ID och hemligheten som också används för att skapa en autentiseringsuppgift i SQL Server. 

* Du måste auktorisera den här nya klient-ID har följande åtkomstbehörighet: **kryptera**, **dekryptera**, **wrapKey**, **unwrapKey**, **logga**, och **Kontrollera**. Detta görs med den [Set AzureRmKeyVaultAccessPolicy](https://msdn.microsoft.com/library/azure/mt603625.aspx) cmdlet. Mer information finns i [tillåta program att använda nyckel eller hemlighet](../articles/key-vault/key-vault-get-started.md#authorize).

### <a name="create-a-key-vault"></a>Skapa ett nyckelvalv
För att kunna använda Azure Key Vault för lagring av nycklar som ska användas för kryptering i den virtuella datorn behöver åtkomst till en nyckelvalvet. Om du redan inte har lagt upp ditt nyckelvalv kan skapa en genom att följa stegen i den [komma igång med Azure Key Vault](../articles/key-vault/key-vault-get-started.md) avsnittet. Observera att det finns vissa information som du behöver för att samla in under den här uppsättningen som krävs senare när du aktiverar Azure Key Vault-integrering på din SQL-VM innan du utför de här stegen.

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

När du kommer att skapa ett nyckelvalv steg, notera den returnerade **vaultUri** -egenskap som är key vault-URL. I exemplet i det steg som visas nedan, namnet på nyckelvalv är ContosoKeyVault, därför nyckelvalvet URL: en skulle vara https://contosokeyvault.vault.azure.net/.

Key vault-URL är tilldelad senare till den **$akvURL** parameter i PowerShell-skript för att aktivera Azure Key Vault-integrering.

När du har skapat nyckelvalvet vi behöver lägga till en nyckel i nyckelvalvet blir den här nyckeln som anges när vi skapar en asymmetrisk nyckel skapa senare i SQL Server.
