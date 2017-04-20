
## <a name="start-your-powershell-session"></a>Starta din PowerShell-session
Först behöver du ha den senaste Azure PowerShell installerad och igång. Mer information finns i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs).

> [!NOTE]
> Många nya funktioner i SQL Database stöds endast när du använder [Azure Resource Manager-distributionsmodellen](../articles/azure-resource-manager/resource-group-overview.md), så exemplen använder [Azure SQL Database PowerShell-cmdletar](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx) för Resource Manager. Service Managements (klassiska) distributionsmodell [Azure SQL Database Service Management-cmdletar](https://msdn.microsoft.com/library/azure/dn546723\(v=azure.300\).aspx) stöds för bakåtkompatibilitet, men vi rekommenderar att du använder Resource Manager-cmdletar.
> 
> 

Kör cmdlet:en [**Add-AzureRmAccount**](https://msdn.microsoft.com/library/azure/mt619267\(v=azure.300\).aspx) för att få fram en inloggningsskärm där du fyller i dina autentiseringsuppgifter. Använd samma autentiseringsuppgifter som du använder för att logga in på Azure Portal.

```PowerShell
Add-AzureRmAccount
```

Om du har flera prenumerationer använder du cmdlet:en [**Set-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619263\(v=azure.300\).aspx) för att välja vilken prenumeration som din PowerShell-session ska använda. För att se vilken prenumeration den nuvarande PowerShell-sessionen använder kör du cmdleten [**Get-AzureRmContext**](https://msdn.microsoft.com/library/azure/mt619265\(v=azure.300\).aspx). För att se alla dina prenumerationer kör du [**Get-AzureRmSubscription**](https://msdn.microsoft.com/library/azure/mt619284\(v=azure.300\).aspx).

```PowerShell
Set-AzureRmContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```
