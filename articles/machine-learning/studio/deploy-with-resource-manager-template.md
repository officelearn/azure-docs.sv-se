---
Rubrik: Distribuera Studio-arbetsyta med Azure Resource Manager titleSuffix: Beskrivning av Azure Machine Learning Studio: Så här distribuerar du en arbetsyta för Azure Machine Learning med hjälp av tjänster för Azure Resource Manager-mall: machine learning ms.service: machine learning ms.component: studio ms.topic: artikel

author: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 02/05/2018
---

# <a name="deploy-azure-machine-learning-studio-workspace-using-azure-resource-manager"></a>Distribuera Azure Machine Learning Studio-arbetsyta med Azure Resource Manager

Med en Azure Resource Manager-mall för distribution av sparar du tid genom att ge ett skalbart sätt att distribuera sammankopplade komponenter med en verifiering och återförsöksmekanism. Om du vill konfigurera Azure Machine Learning-arbetsytor, till exempel behöver du du först konfigurera ett Azure storage-konto och sedan distribuerar din arbetsyta. Tänk dig att göra detta manuellt för hundratals arbetsytor. Ett enklare alternativ är att använda en Azure Resource Manager-mall för att distribuera en Azure Machine Learning-arbetsyta och alla dess beroenden. Den här artikeln tar dig igenom processen steg för steg. En bra översikt över Azure Resource Manager finns i [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Steg för steg: skapa en Machine Learning-arbetsyta
Vi ska skapa en Azure-resursgrupp och sedan distribuera ett nytt Azure storage-konto och ett nytt Azure Machine Learning-arbetsyta med en Resource Manager-mall. När distributionen är klar kan ut vi viktig information om arbetsytor som har skapats (primärnyckel, workspaceID och URL: en till arbetsytan).

### <a name="create-an-azure-resource-manager-template"></a>Skapa en Azure Resource Manager-mall
En Machine Learning-arbetsyta kräver ett Azure storage-konto för att lagra den datauppsättning som är länkad till den.
Följande mall använder namnet på resursgruppen som ska generera lagringskontonamn och namnet på arbetsytan.  Den använder också namnet på lagringskontot som en egenskap när du skapar arbetsytan.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Spara den här mallen som mlworkspace.json filen under c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Distribuera resursgruppen, baserat på mallen
* Öppna PowerShell
* Installera moduler för Azure Resource Manager och Azure Service Management  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   De här stegen hämta och installera modulerna som krävs för att utföra stegen. Detta behöver bara göras en gång i miljön där du kör PowerShell-kommandon.   

* Autentisera till Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Connect-AzureRmAccount
```
Det här steget måste du upprepa för varje session. När autentiseringen är klar visas din prenumerationsinformation.

! [Azure-konto] [1]

Nu när vi har åtkomst till Azure kan vi skapa resursgruppen.

* Skapa en resursgrupp

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Kontrollera att resursgruppen har etablerats korrekt. **ProvisioningState** ska vara ”lyckades”.
Resursgruppens namn används av mallen för att generera lagringskontonamnet. Lagringskontonamnet måste vara mellan 3 och 24 tecken och siffror och gemener.

![Resource Group][2]

* Med distributionen av resursgrupper kan distribuera en ny Machine Learning-arbetsyta.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

När distributionen är klar, är det enkelt att egenskaper i arbetsytan som du har distribuerat. Du kan till exempel komma åt den primära nyckeln-Token.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Ett annat sätt att hämta token på befintlig arbetsyta är att använda kommandot Invoke-AzureRmResourceAction. Du kan exempelvis visa de primära och sekundära token för alla arbetsytor.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
När arbetsytan har etablerats, du kan även automatisera många Azure Machine Learning Studio-uppgifter med hjälp av den [PowerShell-modulen för Azure Machine Learning](https://aka.ms/amlps).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [skapa Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-authoring-templates.md). 
* En titt på de [Azure Snabbstartsmallarna på lagringsplatsen](https://github.com/Azure/azure-quickstart-templates). 
* Den här videon om [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 
* Se den [Resource Manager-mall referenshjälp](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions) 
 <!--Image references--> [1]:./media/deploy-with-resource-manager-template/azuresubscription.png [2]: ./media/deploy-with-resource-manager-template/ resourcegroupprovisioning.PNG


<!--Link references-->
