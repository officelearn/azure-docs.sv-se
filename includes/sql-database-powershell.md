
## Starta din PowerShell-session

[Azure PowerShell](https://msdn.microsoft.com/library/mt619274.aspx) (1.0 eller senare) måste vara installerat och igång. Mer information finns i [Installera och konfigurera Azure PowerShell](../articles/powershell-install-configure.md).


>[AZURE.NOTE] Många nya funktioner i SQL Database stöds endast med [Azure Resource Manager-distributionsmodellen](../articles/resource-group-overview.md), så exemplen använder [Azure SQL Database PowerShell-cmdletar](https://msdn.microsoft.com/library/azure/mt574084.aspx) för Resource Manager. Den befintliga klassiska distributionsmodellen [Azure SQL Database (klassisk)-cmdletar](https://msdn.microsoft.com/library/azure/dn546723.aspx) stöds för bakåtkompatibilitet, men vi rekommenderar att du använder Resource Manager-cmdletar. 


Kör cmdleten [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/mt619267.aspx) för att få fram en inloggningsskärm där du fyller i dina autentiseringsuppgifter. Använd samma autentiseringsuppgifter som du använder för att logga in på Azure-portalen.

    Add-AzureRmAccount

Om du har flera prenumerationer använder du cmdleten [**Set-AzureRmContext**](https://msdn.microsoft.com/library/mt619263.aspx) för att välja vilken prenumeration som din PowerShell-session ska använda. För att se vilken prenumeration den nuvarande PowerShell-sessionen använder kör du cmdleten [**Get-AzureRmContext**](https://msdn.microsoft.com/library/mt619265.aspx). För att se alla dina prenumerationer kör du [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/mt619284.aspx).

    Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'




<!--HONumber=Jun16_HO2-->


