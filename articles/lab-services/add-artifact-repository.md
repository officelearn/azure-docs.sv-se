---
title: Lägga till en artefaktcentrallagret i ditt labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du lägger till en artefaktcentrallagret i ditt labb i Azure DevTest labs.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: c1e74efa9cf99e8510ea17aedc840ce3b0731c3b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916682"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Lägga till en artefaktcentrallagret i ditt labb i DevTest Labs
DevTest Labs kan du ange en artefakt som ska läggas till en virtuell dator vid tidpunkten för att skapa den virtuella datorn eller när den virtuella datorn har skapats. Den här artefakten kan vara ett verktyg eller ett program som du vill installera på den virtuella datorn. Artefakter har definierats i en JSON-fil lästs in från en GitHub- eller VSTS Git-lagringsplats. 

Den [offentliga artefaktlagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), underhålls av DevTest Labs, ger många vanliga verktyg för både Windows och Linux. En länk till den här lagringsplatsen läggs automatiskt till labbet. Du kan skapa egna artefaktcentrallagret med särskilda verktyg som inte är tillgängliga i den offentliga artefaktlagringsplatsen. Läs om hur du skapar anpassade artefakter i [skapa anpassade artefakter](devtest-lab-artifact-author.md).

Den här artikeln innehåller information om hur du lägger till anpassad artefakt databasen med hjälp av Azure portal, Azure Resource Management-mallar och Azure PowerShell. Du kan automatisera att lägga till en artefaktcentrallagret i ett labb genom att skriva skript med PowerShell eller CLI. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter
Om du vill lägga till en lagringsplats i labbet, först få viktig information från databasen. I följande avsnitt beskrivs hur du hämtar information som behövs för databaser som finns på **GitHub** eller **Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Hämta GitHub-lagringsplatsen klon-URL och personlig åtkomst-token

1. Gå till startsidan för GitHub-lagringsplatsen som innehåller artefakten eller Malldefinitioner för Resource Manager.
2. Välj **Klona eller ladda ned**.
3. URL: en till Urklipp och markera den **url för HTTPS-klon** knappen. Spara URL: en för senare användning.
4. Välj en avbildning med profilen i det övre högra hörnet på GitHub och välj sedan **inställningar**.
5. I den **personliga inställningar** menyn till vänster, Välj **utvecklarinställningar**.
6. Välj **personliga åtkomsttoken** på den vänstra menyn.
7. Välj **Generera ny token**.
8. På den **ny personlig åtkomsttoken** sidan under **Tokenbeskrivning**, ange en beskrivning. Acceptera standardobjekten under **Välj områden**, och välj sedan **generera Token**.
9. Spara den genererade token. Du använder token senare.
10. Stäng GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Få Azure-lagringsplatser klonen URL och personlig åtkomsttoken
1. Gå till startsidan för din team-samling (till exempel https://contoso-web-team.visualstudio.com), och välj sedan ditt projekt.
2. Välj på projektets startsida, **kod**.
3. Visa klon-URL på projektet **kod** väljer **klona**.
4. Spara URL: en. Du använder URL: en senare.
5. Om du vill skapa en personlig åtkomsttoken i användarens konto nedrullningsbara menyn, Välj **min profil**.
6. På sidan profil information väljer **Security**.
7. På den **Security** fliken **Lägg till**.
8. På den **skapa en personlig åtkomsttoken** sidan:
   1. Ange en **beskrivning** för token.
   2. I den **upphör att gälla i** väljer **180 dagar**.
   3. I den **konton** väljer **alla tillgängliga konton**.
   4. Välj den **alla omfattningar** alternativet.
   5. Välj **skapa Token**.
9. Den nya token visas i den **personliga åtkomsttoken** lista. Välj **kopiera Token**, och spara token-värde för senare användning.
10. Fortsätta att ansluta ditt labb till avsnittet för databasen.

## <a name="use-azure-portal"></a>Använda Azure-portalen
Det här avsnittet innehåller steg för att lägga till en artefaktcentrallagret till ett labb i Azure-portalen. 

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj **fler tjänster**, och välj sedan **DevTest Labs** från listan över tjänster.
3. Listan över labbar, väljer du ditt labb. 
4. Välj **konfiguration och principer** på den vänstra menyn.
5. Välj **databaser** under **externa resurser** avsnitt i den vänstra menyn.
6. Välj **+ Lägg till** i verktygsfältet.

    ![Knappen Lägg till databasen](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. På den **databaser** anger du följande information:
   1. **Namn**. Ange ett namn för databasen.
   2. **Url för Git-klonen**. Ange URL för Git HTTPS-klonen som du kopierade tidigare från GitHub eller Azure DevOps-tjänsterna.
   3. **Branch**. Ange grenen för att få definitionerna.
   4. **Personlig åtkomsttoken**. Ange din personliga åtkomsttoken som du tidigare fått från GitHub eller Azure DevOps-tjänsterna.
   5. **Mappsökvägar**. Ange minst en mappsökväg i förhållande till den klon-URL som innehåller din artefakt eller Malldefinitioner för Resource Manager. När du anger en underkatalog måste du kontrollera att du inkluderar snedstreck i mappsökvägen.

        ![Lagringsplatser området](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Välj **Spara**.

## <a name="use-azure-resource-manager-template"></a>Använd Azure Resource Manager-mallar
Azure Resource Management (Azure Resource Manager)-mallar är JSON-filer som beskriver resurser i Azure som du vill skapa. Mer information om dessa mallar finns i [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).

Det här avsnittet innehåller steg för att lägga till en artefaktcentrallagret till ett labb med hjälp av en Azure Resource Manager-mall.  Mallen skapar labbet om den inte redan finns. 

### <a name="template"></a>Mall
Exempelmallen används i den här artikeln samlar in följande information via parametrar. De flesta av parametrarna behöver smarta standardvärden, men det finns några värden som måste anges. Du måste ange labbnamn URI för artefaktcentrallagret och säkerhetstoken för databasen. 

- Labbnamn.
- Visningsnamn för artefaktcentrallagret i DevTest Labs-användargränssnittet (UI). Standardvärdet är: `Team Repository`.
- URI till lagringsplatsen (exempel: `https://github.com/<myteam>/<nameofrepo>.git` eller `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- Gren i databasen som innehåller artefakter. Standardvärdet är: `master`.
- Namnet på den mapp som innehåller artefakter. Standardvärdet är: `/Artifacts`.
- Typ av databasen. Tillåtna värden är `VsoGit` eller `GitHub`.
- Åtkomsttoken för databasen. 

    ```json
    {
    
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>Distribuera mallen
Det finns ett antal sätt att distribuera mallen till Azure och få resursen skapas, om det inte finns eller uppdateras, om den finns. Mer information finns i följande artiklar:

- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [Distribuera resurser med Resource Manager-mallar och Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Distribuera resurser med Resource Manager-mallar och Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

Vi går vidare och se hur du distribuerar mallen i PowerShell. Cmdlets som används för att distribuera mallen är sammanhangsberoende, så aktuella klienten och aktuella prenumerationen används. Använd [Set-AzContext](/powershell/module/az.accounts/set-azcontext) innan du distribuerar mallen, vid behov för att ändra kontexten.

Börja med att skapa en resurs med [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Om resursgruppen som du vill använda redan finns, kan du hoppa över det här steget.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Därefter skapar du en distribution till en resurs-grupp med [New AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Denna cmdlet gäller resource ändringarna till Azure. Flera resource-distributioner kan göras för en viss resurs-grupp. Om du distribuerar flera gånger till samma resursgrupp, kontrollera att namnet på varje distribution är unika.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

När ny AzResourceGroupDeployment körs visar kommandot viktig information som Etableringsstatus (bör vara klar) och utdata för mallen.
 
## <a name="use-azure-powershell"></a>Använda Azure PowerShell 
Det här avsnittet innehåller ett PowerShell-skript som kan användas för att lägga till en artefaktcentrallagret i ett labb. Om du inte har Azure PowerShell, se [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) för detaljerade anvisningar för att installera den.

### <a name="full-script"></a>Fullständigt skript
Här är fullständigt skript, inklusive vissa utförliga meddelanden och kommentarer:

**New-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab. 

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>

 
[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',
    
    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,
    
    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"@

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"
 
Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>Kör PowerShell-skriptet
I följande exempel visas hur du kör skriptet: 

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parametrar
PowerShell-exempelskriptet i den här artikeln använder följande parametrar:

| Parameter | Beskrivning | 
| --------- | ----------- | 
| LabName | Namnet på labbet. |
| ArtifactRepositoryName | Namn för den nya artefaktcentrallagret. Skriptet skapar ett slumpmässigt namn för databasen sparas om det inte har angetts. |
| ArtifactRepositoryDisplayName | Visningsnamn för artefaktcentrallagret. Detta är det namn som visas i Azure-portalen (https://portal.azure.com) när du visar alla artefakt lagringsplatser för ett labb. |
| RepositoryUri | URI till lagringsplatsen. Exempel: `https://github.com/<myteam>/<nameofrepo>.git` eller `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.| 
| RepositoryBranch | Gren i vilka artefakten filer kan hittas. Standardvärdet är ”master”. | 
| FolderPath | Mapp där artefakter kan hittas. Som standard ' / artefakter |
| PersonalAccessToken | Säkerhetstoken för att komma åt databasen GitHub eller VSOGit. Se avsnittet förutsättningar anvisningar att hämta personliga åtkomsttoken |
| sourceType | Om artefakten är VSOGit eller GitHub-lagringsplats. |

Själva lagringsplatsen måste ett internt namn för identifiering, vilket skiljer sig som visningsnamnet som visas i Azure-portalen. Du ser inte det interna namnet med hjälp av Azure portal, men visas när du använder Azure REST API: er eller Azure PowerShell. Skriptet innehåller ett namn, om inget annat anges av användaren av våra skript.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>PowerShell-kommandon som används i skriptet

| PowerShell-kommando | Anteckningar |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Det här kommandot används för att få information om labbet, till exempel dess plats. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Det finns inga specifika kommando för att lägga till artefakt databaser. Allmän [New AzResource](/powershell/module/az.resources/new-azresource) cmdlet gör jobbet. Denna cmdlet måste antingen den **ResourceId** eller **ResourceName** och **ResourceType** par veta vilken typ av resurs som ska skapas. Det här exempelskriptet använder resursnamnet och par för typ av resurs. <br/><br/>Observera att du skapar lagerkälla artefakt på samma plats och under samma resursgrupp som labbet.|

Skriptet lägger till en ny resurs i den aktuella prenumerationen. Använd [Get-AzContext](/powershell/module/az.accounts/get-azcontext) se den här informationen. Använd [Set-AzContext](/powershell/module/az.accounts/set-azcontext) att ange aktuell klient och prenumeration.

Det bästa sättet att identifiera resursens namn och information om resursen är att använda den [Test Drive Azure REST API: er](https://azure.github.io/projects/apis/) webbplats. Kolla in den [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) provider för att se tillgängliga REST API: er för DevTest Labs-providern. Skriptanvändare följande resurs-ID. 

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```
 
Resurstypen är allt som anges efter ”providers” i URI: N, förutom objekten i klammerparenteser. Resursnamnet är allt visas i klammerparenteser. Om mer än ett objekt kan förväntas för resursnamnet, Avgränsa varje objekt med ett snedstreck som vi har gjort. 

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Nästa steg
- [Ange obligatoriska artefakter för ditt labb i Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Skapa anpassade artefakter för din virtuella dator för DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnostisera fel i artefakter i laboratoriet](devtest-lab-troubleshoot-artifact-failure.md)

