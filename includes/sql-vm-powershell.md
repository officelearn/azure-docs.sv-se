
## <a name="start-your-powershell-session"></a>Starta din PowerShell-session
Först måste du ha senast [Azure PowerShell](http://msdn.microsoft.com/library/mt619274.aspx) installerad och körs. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Exemplen i det här avsnittet använder [Azure Resource Manager-distributionsmodellen](../articles/azure-resource-manager/resource-group-overview.md), så exemplen använder den [Azure Resource Manager cmdlets](http://msdn.microsoft.com/library/azure/mt125356.aspx). 
> 
> 

Kör den [ **Add-AzureRmAccount** ](http://msdn.microsoft.com/library/mt619267.aspx) cmdlet och du kommer att visas ett tecken på skärmen för att ange dina autentiseringsuppgifter. Använd samma autentiseringsuppgifter som du använder för att logga in på Azure-portalen.

    Add-AzureRmAccount

Om du har flera prenumerationer använder den [ **Set-AzureRmContext** ](http://msdn.microsoft.com/library/mt619263.aspx) för att välja vilken prenumeration som din PowerShell-session ska använda. För att se vilken prenumeration den nuvarande PowerShell-sessionen använder kör du cmdleten [**Get-AzureRmContext**](http://msdn.microsoft.com/library/mt619265.aspx). För att se alla dina prenumerationer kör du [**Get-AzureRmSubscription**](http://msdn.microsoft.com/library/mt619284.aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'

