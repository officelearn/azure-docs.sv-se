## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Login-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Login-AzureRmAccount
```

Om du inte vet vilken plats som du vill använda, kan du visa tillgängliga platser. När listan visas hitta det du vill använda. Det här exemplet används **eastus**. Lagra den i en variabel och Använd variabeln så att du kan ändra den på en plats.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. 

```powershell
$resourceGroup = "myResourceGroup"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 
```

## <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett allmänt lagringskonto som standard med LRS replikering [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount), hämta kontexten för lagringskontot som definierar lagringskontot som ska användas. När fungerar på ett lagringskonto, referera kontexten i stället för att tillhandahålla autentiseringsuppgifterna flera gånger. Det här exemplet skapar ett lagringskonto som kallas *mittlagringskonto* med lokalt redundant lagring och blob-kryptering aktiverat.

```powershell
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx = $storageAccount.Context
```
