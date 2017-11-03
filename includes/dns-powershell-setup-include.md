## <a name="set-up-azure-powershell-for-azure-dns"></a>Konfigurera Azure PowerShell för Azure DNS

### <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har följande innan du påbörjar konfigurationen.

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
* Du måste installera den senaste versionen av Azure Resource Manager PowerShell-cmdlets. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).

### <a name="sign-in-to-your-azure-account"></a>Logga in på ditt Azure-konto

Öppna PowerShell-konsolen och anslut till ditt konto. Mer information finns i [med hjälp av PowerShell med Resource Manager](../articles/azure-resource-manager/powershell-azure-resource-manager.md).

```powershell
Login-AzureRmAccount
```

### <a name="select-the-subscription"></a>Välja prenumerationen
 
Kontrollera prenumerationerna för kontot.

```powershell
Get-AzureRmSubscription
```

Välj vilka av dina Azure-prenumerationer som du vill använda.

```powershell
Select-AzureRmSubscription -SubscriptionName "your_subscription_name"
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standardplats för resurserna i den resursgruppen. Men eftersom alla DNS-resurser är globala, inte regionala, så påverkar inte valet av resursgruppens plats Azure DNS.

Du kan hoppa över det här steget om du använder en befintlig resursgrupp.

```powershell
New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"
```

### <a name="register-resource-provider"></a>Registrera resursprovider

Azure DNS-tjänsten hanteras av Microsoft.Network-resursprovidern. Din Azure-prenumeration måste vara registrerad att använda den här resursprovidern innan du kan använda Azure DNS. Det här är en engångsåtgärd för varje prenumeration.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```