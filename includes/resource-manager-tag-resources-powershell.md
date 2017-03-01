Version 3.0 av modulen AzureRm.Resources innehåller viktiga ändringar när det gäller hur du arbetar med taggar. Kontrollera vilken version du har innan du fortsätter:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Om resultatet visar version 3.0 eller senare fungerar exemplen i det här avsnittet med din miljö. Om du inte har version 3.0 eller senare måste du [uppdatera din version](/powershell/azureps-cmdlets-docs/) med hjälp av PowerShell-galleriet eller installationsprogrammet för webbplattform innan du fortsätter med det här avsnittet.

```powershell
Version
-------
3.5.0
```

Varje gång du använder taggar i en resurs eller resursgrupp skriver du över de befintliga taggarna för den resursen eller resursgruppen. Därför måste du använda ett annat tillvägagångssätt beroende på om resursen eller resursgruppen har taggar som du vill behålla. Så här lägger du till taggar till en

* resursgrupp utan befintliga taggar.

  ```powershell
  Set-AzureRmResourceGroup -Name TagTestGroup -Tag @{ Dept="IT"; Environment="Test" }
  ```

* resursgrupp med befintliga taggar.

  ```powershell
  $tags = (Get-AzureRmResourceGroup -Name TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResourceGroup -Tag $tags -Name TagTestGroup
  ```

* resurs utan befintliga taggar.

  ```powershell
  Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

* resurs med befintliga taggar.

  ```powershell
  $tags = (Get-AzureRmResource -ResourceName storageexample -ResourceGroupName TagTestGroup).Tags
  $tags += @{Status="Approved"}
  Set-AzureRmResource -Tag $tags -ResourceName storageexample -ResourceGroupName TagTestGroup -ResourceType Microsoft.Storage/storageAccounts
  ```

Om du vill tillämpa alla taggar från en resursgrupp på dess resurser **utan att behålla någon av de befintliga taggarna för resurserna** kan du använda följande skript:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force } 
}
```

Om du vill tillämpa alla taggar från en resursgrupp på dess resurser och **behålla de befintliga taggarna på resurser som inte är dubbletter** kan du använda följande skript:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    if ($g.Tags -ne $null) {
        $resources = Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName 
        foreach ($r in $resources)
        {
            $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
            foreach ($key in $g.Tags.Keys)
            {
                if ($resourcetags.ContainsKey($key)) { $resourcetags.Remove($key) }
            }
            $resourcetags += $g.Tags
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Om du vill ta bort alla taggar skickar du en tom hash-tabell.

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name TagTestGgroup
```

Om du vill hämta resursgrupper med en viss tagg använder du cmdlet:en `Find-AzureRmResourceGroup`.

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

Om du vill hämta alla resurser med en viss tagg och ett visst värde använder du cmdlet:en `Find-AzureRmResource`.

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```



<!--HONumber=Feb17_HO3-->


