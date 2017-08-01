Version 3.0 av modulen AzureRm.Resources innehåller viktiga ändringar när det gäller hur du arbetar med taggar. Innan du fortsätter kontrollerar du din version:

```powershell
Get-Module -ListAvailable -Name AzureRm.Resources | Select Version
```

Om resultatet visar version 3.0 eller senare fungerar exemplen i det här avsnittet med din miljö. Om du inte har version 3.0 eller senare måste du [uppdatera versionen](/powershell/azureps-cmdlets-docs/) med hjälp av PowerShell-galleriet eller med Installationsprogram för webbplattform innan du fortsätter med det här avsnittet.

```powershell
Version
-------
3.5.0
```

Om du vill visa de befintliga taggarna för en *resursgrupp* använder du:

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

Skriptet returnerar följande format:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Om du vill visa de befintliga taggarna för en *resurs som har ett angivet resurs-ID* använder du:

```powershell
(Get-AzureRmResource -ResourceId {resource-id}).Tags
```

Och om du vill visa de befintliga taggarna för en *resurs som har ett angivet namn och en angiven resursgrupp* använder du:

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Om du vill hämta *resursgrupper som har en specifik tagg* använder du:

```powershell
(Find-AzureRmResourceGroup -Tag @{ Dept="Finance" }).Name 
```

Om du vill hämta *resurser som har en specifik tagg* använder du:

```powershell
(Find-AzureRmResource -TagName Dept -TagValue Finance).Name
```

Varje gång du tillämpar taggar på en resurs eller resursgrupp skriver du över resursens eller resursgruppens befintliga taggar. Därför måste du använda ett annat tillvägagångssätt beroende på om resursen eller resursgruppen har befintliga taggar. 

Om du vill lägga till taggar till en *resursgrupp utan befintliga taggar* använder du:

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Om du vill lägga till taggar till en *resursgrupp som har befintliga taggar* hämtar du de befintliga taggarna, lägger till den nya taggen och tillämpar taggarna igen:

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Om du vill lägga till taggar till en *resurs utan befintliga taggar* använder du:

```powershell
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceName examplevnet -ResourceGroupName examplegroup
```

Om du vill lägga till taggar till en *resurs som har befintliga taggar* använder du:

```powershell
$tags = (Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
$tags += @{Status="Approved"}
Set-AzureRmResource -Tag $tags -ResourceName examplevnet -ResourceGroupName examplegroup
```

Om du vill tillämpa alla taggar från en resursgrupp på dess resurser *utan att behålla någon av de befintliga taggarna för resurserna* kan du använda följande skript:

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups) 
{
    Find-AzureRmResource -ResourceGroupNameEquals $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force } 
}
```

Om du vill tillämpa alla taggar från en resursgrupp på dess resurser och *behålla de befintliga taggarna på resurser som inte är dubbletter* kan du använda följande skript:

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

Om du vill ta bort alla taggar skickar du en tom hash-tabell:

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```



