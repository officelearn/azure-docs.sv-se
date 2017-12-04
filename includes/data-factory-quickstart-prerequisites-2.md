### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-powershell"></a>Installera PowerShell
Installera den senaste versionen av PowerShell om du inte har den på din dator. 

1. Öppna webbläsaren och gå till sidan [Ladda ned Azure-SDK:er och verktyg](https://azure.microsoft.com/downloads/). 
2. Klicka på **Windows-installation** i avsnittet **Kommandoradsverktyg** -> **PowerShell**. 
3. Kör **MSI**-filen för att installera PowerShell. 

Mer detaljerade anvisningar finns i [Installera och konfigurera PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Logga in på PowerShell

1. Starta **PowerShell** på din dator. Låt PowerShell vara öppet tills du är klar med snabbstarten. Om du stänger och öppnar det igen måste du köra kommandona en gång till.

    ![Starta PowerShell](media/data-factory-quickstart-prerequisites-2/search-powershell.png)
1. Kör följande kommando och ange användarnamnet och lösenordet som du använder för att logga in på Azure-portalen:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Om du har flera Azure-prenumerationer kör du följande kommando för att visa alla prenumerationer kopplade till det här kontot:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Kör följande kommando för att välja den prenumeration som du vill arbeta med. Ersätt **SubscriptionId** med ID:t för din Azure-prenumeration:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```
