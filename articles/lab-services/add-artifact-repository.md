---
title: Lägga till en artefaktdatabas i labbet i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du lägger till en artefaktdatabas i labbet i Azure DevTest.
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
ms.openlocfilehash: a0dbd92533703a56f1ec2478fab8944656129247
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295514"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Lägga till en artefaktdatabas i labbet i DevTest Labs
DevTest Labs kan du ange en artefakt som ska läggas till en virtuell dator vid tidpunkten för skapandet av den virtuella datorn eller efter att den virtuella datorn har skapats. Den här artefakten kan vara ett verktyg eller ett program som du vill installera på den virtuella datorn. Artefakter definieras i en JSON-fil som läses in från en GitHub- eller Azure DevOps Git-databas.

Den [offentliga artefaktarkivet](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), som underhålls av DevTest Labs, innehåller många vanliga verktyg för både Windows och Linux. En länk till den här databasen läggs automatiskt till i labbet. Du kan skapa en egen artefaktdatabas med specifika verktyg som inte är tillgängliga i den offentliga artefaktdatabasen. Mer information om hur du skapar anpassade artefakter finns i [Skapa anpassade artefakter](devtest-lab-artifact-author.md).

Den här artikeln innehåller information om hur du lägger till din anpassade artefaktdatabas med hjälp av Azure Portal, Azure Resource Management-mallar och Azure PowerShell. Du kan automatisera lägger till en artefaktdatabas i ett labb genom att skriva PowerShell- eller CLI-skript.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav
Om du vill lägga till en databas i labbet får du först viktig information från databasen. I följande avsnitt beskrivs hur du hämtar den information som krävs för databaser som finns på **GitHub** eller **Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Hämta GitHub-databasklonadressen och den personliga åtkomsttoken

1. Gå till startsidan för GitHub-databasen som innehåller malldefinitionerna för artefakt eller Resurshanteraren.
2. Välj **Klona eller ladda ned**.
3. Om du vill kopiera URL:en till Urklipp markerar du **url-knappen för HTTPS-klon.** Spara webbadressen för senare användning.
4. I det övre högra hörnet av GitHub markerar du profilbilden och väljer sedan **Inställningar**.
5. Välj **Utvecklarinställningar**på menyn **Personliga inställningar** till vänster .
6. Välj **Personliga åtkomsttoken på** den vänstra menyn.
7. Välj **Generera ny token**.
8. Ange en beskrivning under **Token beskrivning**på sidan Ny **personlig åtkomsttoken.** Acceptera standardobjekten under **Välj scope**och välj sedan **Generera token**.
9. Spara den genererade token. Du använder token senare.
10. Stäng GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Hämta URL:en för Klon av Azure Repos och token för personlig åtkomst
1. Gå till startsidan för gruppsamlingen `https://contoso-web-team.visualstudio.com`(till exempel ) och välj sedan projektet.
2. Välj **Kod**på projektets startsida .
3. Om du vill visa klon-URL:en väljer du **Klona**på sidan **Projektkod.**
4. Spara webbadressen. Du använder webbadressen senare.
5. Om du vill skapa en personlig åtkomsttoken väljer du **Min profil**i den nedrullningsbara menyn Användarkonto .
6. På sidan med profilinformation väljer du **Säkerhet**.
7. Välj **Lägg till**på fliken **Säkerhet** .
8. På sidan **Skapa en personlig åtkomsttoken:**
   1. Ange en **beskrivning** för token.
   2. Välj **180 dagar**i listan **Upphör att gälla** i .
   3. I listan **Konton** väljer du **Alla tillgängliga konton**.
   4. Välj alternativet **Alla scope.**
   5. Välj **Skapa token**.
9. Den nya token visas i listan **Token för personlig åtkomst.** Välj **Kopiera token**och spara sedan tokenvärdet för senare användning.
10. Fortsätt till avsnittet Anslut labbet till databasavsnittet.

## <a name="use-azure-portal"></a>Använda Azure-portalen
Det här avsnittet innehåller steg för att lägga till en artefaktdatabas i ett labb i Azure-portalen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Fler tjänster**och välj sedan **DevTest Labs** i listan över tjänster.
3. Välj ditt labb i listan över labb.
4. Välj **Konfiguration och principer** på den vänstra menyn.
5. Välj **Databaser** under avsnittet **Externa resurser** på den vänstra menyn.
6. Välj **+ Lägg till** i verktygsfältet.

    ![Knappen Lägg till databas](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. På sidan **Databaser** anger du följande information:
   1. **Namn**. Ange ett namn på databasen.
   2. **Git Klon Url**. Ange Git HTTPS-klon-URL:en som du kopierade tidigare från GitHub eller Azure DevOps Services.
   3. **Gren**. Ange grenen för att hämta definitionerna.
   4. **Token för personlig åtkomst**. Ange den personliga åtkomsttoken som du fick tidigare från antingen GitHub eller Azure DevOps Services.
   5. **Mappsökvägar**. Ange minst en mappsökväg i förhållande till klon-URL:en som innehåller malldefinitionerna för artefakten eller Resurshanteraren. När du anger en underkatalog kontrollerar du att du inkluderar det vidarebefordrade snedstrecket i mappsökvägen.

        ![Området Databaser](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Välj **Spara**.

## <a name="use-azure-resource-manager-template"></a>Använda Azure Resource Manager-mall
Azure Resource Management (Azure Resource Manager) mallar är JSON-filer som beskriver resurser i Azure som du vill skapa. Mer information om dessa mallar finns i [Skapa Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md).

Det här avsnittet innehåller steg för att lägga till en artefaktdatabas i ett labb med hjälp av en Azure Resource Manager-mall.  Mallen skapar labbet om det inte redan finns.

### <a name="template"></a>Mall
Exempelmallen som används i den här artikeln samlar in följande information via parametrar. De flesta parametrar har smarta standardvärden, men det finns några värden som måste anges. Du måste ange labbnamnet, URI för artefaktdatabasen och säkerhetstoken för databasen.

- Labbnamn.
- Visningsnamn för artefaktdatabasen i användargränssnittet för DevTest Labs (UI). Standardvärdet är: `Team Repository`.
- URI till databasen (Exempel: `https://github.com/<myteam>/<nameofrepo>.git` eller `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- Gren i databasen som innehåller artefakter. Standardvärdet är: `master`.
- Namn på mappen som innehåller artefakter. Standardvärdet är: `/Artifacts`.
- Typ av databas. Tillåtna `VsoGit` värden `GitHub`är eller .
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
Det finns några sätt att distribuera mallen till Azure och få resursen skapad, om den inte finns eller uppdateras, om den finns. Mer information finns i följande artiklar:

- [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
- [Distribuera resurser med Resource Manager-mallar och Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
- [Distribuera resurser med Resource Manager-mallar och Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)

Låt oss gå vidare och se hur du distribuerar mallen i PowerShell. Cmdlets som används för att distribuera mallen är kontextspecifika, så aktuella klient- och aktuella prenumerationer används. Använd [Set-AzContext](/powershell/module/az.accounts/set-azcontext) innan du distribuerar mallen, om det behövs, för att ändra kontexten.

Skapa först en resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Om den resursgrupp du vill använda redan finns hoppar du över det här steget.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Skapa sedan en distribution till resursgruppen med [new-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Den här cmdleten tillämpar resursändringarna på Azure. Flera resursdistributioner kan göras till en viss resursgrupp. Om du distribuerar flera gånger till samma resursgrupp kontrollerar du att namnet på varje distribution är unikt.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

När New-AzResourceGroupDeployment har körts utdata utdata viktig information som etableringstillståndet (bör lyckades) och eventuella utdata för mallen.

## <a name="use-azure-powershell"></a>Använda Azure PowerShell
Det här avsnittet innehåller ett exempel på PowerShell-skript som kan användas för att lägga till en artefaktdatabas i ett labb. Om du inte har Azure PowerShell läser du [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) för detaljerade instruktioner för att installera det.

### <a name="full-script"></a>Fullständigt skript
Här är hela skriptet, inklusive några utförliga meddelanden och kommentarer:

**Ny-DevTestLabArtifactRepository.ps1**:

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

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

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
Exempel på PowerShell-skript i den här artikeln tar följande parametrar:

| Parameter | Beskrivning |
| --------- | ----------- |
| LabName (LabName) | Namnet på labbet. |
| ArtifactRepositoryName | Namn på den nya artefaktdatabasen. Skriptet skapar ett slumpmässigt namn för respository om det inte anges. |
| ArtifactRepositoryDisplayName | Visningsnamn för artefaktdatabasen. Det här är namnet som visashttps://portal.azure.com) i Azure-portalen ( när du visar alla artefaktdatabaser för ett labb. |
| RepositoryUri | Uri till databasen. Exempel: `https://github.com/<myteam>/<nameofrepo>.git` `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`eller .|
| LagringsplatsBranch | Gren där artefaktfiler kan hittas. Standardvärden till "master". |
| FolderPath (FolderPath) | Mapp under vilken artefakter kan hittas. Standardvärden till '/Artefakter' |
| PersonalAccessToken | Säkerhetstoken för åtkomst till GitHub- eller VSOGit-databasen. Se avsnittet för förutsättningar för instruktioner för att få personlig åtkomsttoken |
| SourceType | Oavsett om artefakt är VSOGit eller GitHub-databas. |

Själva databasen behöver ett internt namn för identifiering, vilket är annorlunda än visningsnamnet som visas i Azure-portalen. Du ser inte det interna namnet med Azure-portalen, men du ser det när du använder Azure REST API:er eller Azure PowerShell. Skriptet ger ett namn, om ett inte anges av användaren av vårt skript.

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
| [Ny-AzResource](/powershell/module/az.resources/new-azresource) | Det finns inget specifikt kommando för att lägga till artefaktdatabaser. Den generiska [New-AzResource](/powershell/module/az.resources/new-azresource) cmdlet gör jobbet. Den här cmdleten behöver antingen **Resursen** eller **Resursnamn** och **ResourceType-paren** för att känna till vilken typ av resurs som ska skapas. Med det här exempelskriptet används resursnamnet och resurstypsparet. <br/><br/>Observera att du skapar artefaktdatabaskällan på samma plats och under samma resursgrupp som labbet.|

Skriptet lägger till en ny resurs i den aktuella prenumerationen. Använd [Get-AzContext](/powershell/module/az.accounts/get-azcontext) för att se den här informationen. Använd [Set-AzContext](/powershell/module/az.accounts/set-azcontext) för att ange den aktuella klienten och prenumerationen.

Det bästa sättet att identifiera resursnamn och resurstypsinformation är att använda webbplatsen [För Azure REST-API:er](https://azure.github.io/projects/apis/) för Test Drive. Kolla in [DevTest Labs – 2016-05-15-providern](https://aka.ms/dtlrestapis) för att se tillgängliga REST-API:er för DevTest Labs-leverantören. Skriptet användare följande resurs-ID.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

Resurstypen är allt som anges efter "leverantörer" i URI, med undantag för objekt som anges i de lockiga parenteserna. Resursnamnet är allt som visas i de lockiga parenteserna. Om mer än ett objekt förväntas för resursnamnet, separera varje objekt med ett snedstreck som vi har gjort.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Nästa steg
- [Ange obligatoriska artefakter för ditt labb i Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Skapa anpassade artefakter för din virtuella DevTest Labs-dator](devtest-lab-artifact-author.md)
- [Diagnostisera artefaktfel i labbet](devtest-lab-troubleshoot-artifact-failure.md)
