---
title: Skapa miljöer med flera virtuella datorer och PaaS-resurser med mallar
description: Lär dig hur du skapar miljöer med flera virtuella datorer och PaaS-resurser i Azure DevTest Labs från en Azure Resource Manager-mall
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 1385b20847cf90c212a13591389dfb6cda08432a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169634"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar

Azure DevTest Labs-miljöer gör det möjligt för användare att enkelt distribuera komplexa infrastrukturer på ett konsekvent sätt inom labbets gränser. Du kan använda [Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md) för att skapa miljöer med uppsättningar med resurser i DevTest Labs. Dessa miljöer kan innehålla alla Azure-resurser som Resource Manager-mallar kan skapa.

Du kan enkelt [lägga till en virtuell dator (VM) åt gången](devtest-lab-add-vm.md) i ett labb med hjälp av [Azure-portalen](https://portal.azure.com). Scenarier som webbappar på flera nivåer eller en SharePoint-servergrupp behöver dock en mekanism för att skapa flera virtuella datorer i ett enda steg. Genom att använda Azure Resource Manager-mallar kan du definiera infrastrukturen och konfigurationen av din Azure-lösning och upprepade gånger distribuera flera virtuella datorer i ett konsekvent tillstånd.

Azure Resource Manager-mallar ger också följande fördelar:

- Azure Resource Manager-mallar läses in direkt från din GitHub- eller Azure Repos-källkontrolldatabas.
- Användarna kan skapa en miljö genom att välja en konfigurerad Azure Resource Manager-mall från Azure-portalen, precis som de gör med andra typer av [VM-baser](devtest-lab-comparing-vm-base-image-types.md).
- Du kan etablera Azure PaaS-resurser samt virtuella IaaS-datorer i en miljö från en Azure Resource Manager-mall.
- Du kan spåra kostnaden för miljöer i labbet, förutom enskilda virtuella datorer som skapats av andra typer av baser. PaaS-resurser skapas och visas i kostnadsspårning. Automatisk avstängning av virtuella datorer gäller dock inte för PaaS-resurser.

Mer information om fördelarna med att använda Resource Manager-mallar för att distribuera, uppdatera eller ta bort många labbresurser i en enda åtgärd finns [i Fördelar med att använda Resource Manager-mallar](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager).

> [!NOTE]
> När du använder en Resource Manager-mall som bas för att skapa virtuella labb-datorer finns det vissa skillnader mellan att skapa flera virtuella datorer eller en enda virtuell dator. Mer information finns i [Använda en virtuell dators Azure Resource Manager-mall](devtest-lab-use-resource-manager-template.md).
>

## <a name="use-devtest-labs-public-environments"></a>Använd DevTest Labs offentliga miljöer
Azure DevTest Labs har en [offentlig databas med Azure Resource Manager-mallar](https://github.com/Azure/azure-devtestlab/tree/master/Environments) som du kan använda för att skapa miljöer utan att själv behöva ansluta till en extern GitHub-källa. Den här offentliga databasen liknar den offentliga lagringsplatsen för artefakter som är tillgängliga i Azure-portalen för varje labb som du skapar. Med miljödatabasen kan du snabbt komma igång med författade miljömallar som har få indataparametrar. Dessa mallar ger dig en smidig komma igång-upplevelse för PaaS-resurser inom labb.

I den offentliga databasen har DevTest Labs-teamet och andra skapat och delat ofta använda mallar som Azure Web Apps, Service Fabric Cluster och en utveckling av SharePoint Farm-miljö. Du kan använda dessa mallar direkt eller anpassa dem efter dina behov. Mer information finns i [Konfigurera och använda offentliga miljöer i DevTest Labs](devtest-lab-configure-use-public-environments.md). När du har skapat egna mallar kan du lagra dem i den här databasen för att dela dem med andra eller konfigurera en egen Git-databas.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Skapa egna malldatabaser

Som en av de bästa metoderna med infrastruktur som kod och konfiguration som kod bör du hantera miljömallar i källkontroll. Azure DevTest Labs följer den här metoden och läser in alla Azure Resource Manager-mallar direkt från dina GitHub- eller Azure-databaser. Därför kan du använda Resource Manager-mallar över hela versionscykeln, från testmiljön till produktionsmiljön.

Det finns flera regler att följa för att ordna dina Azure Resource Manager-mallar i en databas:

- Du måste namnge huvudmallfilen *azuredeploy.json*.

- Om du vill använda parametervärden som definierats i en parameterfil måste parameterfilen heta *azuredeploy.parameters.json*.

  Du kan använda `_artifactsLocation` `_artifactsLocationSasToken` parametrarna och konstruera parametrarnaLink URI-värde, så att DevTest Labs automatiskt kan hantera kapslade mallar. Mer information finns i [Distribuera kapslade Azure Resource Manager-mallar för testmiljöer](deploy-nested-template-environments.md).

- Du kan definiera metadata för att ange mallvisningsnamnet och beskrivningen i en fil med namnet *metadata.json*enligt följande:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Viktiga Mallfiler för Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Lägga till malldatabaser i labbet

När du har skapat och konfigurerat databasen kan du lägga till den i labbet med hjälp av Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.
1. Välj det labb du vill använda i listan över labb.
1. I fönstret **Översikt** i labbet väljer du **Konfiguration och principer**.

   ![Konfiguration och principer](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. Välj Databaser i listan Inställningar **för**konfiguration **och principer** . Den **offentliga artefaktrepodatabasen** genereras automatiskt för alla labb och ansluter till [DevTest Labs offentliga GitHub-databas](https://github.com/Azure/azure-devtestlab).

1. Om du vill lägga till malldatabasen för Azure Resource Manager väljer du **Lägg till**.

   ![Offentlig repo](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. Ange följande information i fönstret **Databaser:**

   - **Namn**: Ange ett databasnamn som ska användas i labbet.
   - **Git klon URL:** Ange Git HTTPS klon URL från GitHub eller Azure Repos.
   - **Gren** (valfritt): Ange grennamnet för att komma åt malldefinitionerna för Azure Resource Manager.
   - **Token för personlig åtkomst**: Ange den personliga åtkomsttoken som används för att komma åt din databas på ett säkert sätt.
     - Om du vill hämta din token från Azure Repos väljer du **Användarinställningar** > **säkerhetsidentifieringstoken****Security** > under din profil .
     - Om du vill hämta din token från GitHub väljer du**Personliga åtkomsttoken för**inställningar**för** >  **utvecklare** > under din profil .
   - **Mappsökvägar:** Ange mappsökvägen som är relativ till din Git-klon-URI för antingen dina artefaktdefinitioner eller malldefinitionerna för Azure Resource Manager.

1. Välj **Spara**.

   ![Lägga till ny databas](./media/devtest-lab-create-environment-from-arm/repo-values.png)

När du har lagt till en Azure Resource Manager-mall i labbet kan labbanvändarna skapa miljöer med hjälp av mallen.

## <a name="configure-access-rights-for-lab-users"></a>Konfigurera åtkomsträttigheter för labbanvändare

Labbanvändare har **reader-roll** som standard, så de kan inte ändra resurserna i en miljöresursgrupp. De kan till exempel inte stoppa eller starta sina resurser.

Så här ger du rollen **som deltagare** i labbanvändare så att de kan redigera resurserna i sina miljöer:

1. I [Azure-portalen](https://portal.azure.com)väljer du **Konfiguration och principer**i labbets **översiktsfönster** och väljer sedan **Labbinställningar**.

1. I fönstret **Labbinställningar** väljer du **Deltagare**och väljer sedan **Spara** för att bevilja skrivbehörighet till labbanvändare.

   ![Konfigurera åtkomsträttigheter för labbanvändare](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

I nästa avsnitt går du igenom att skapa miljöer från en Azure Resource Manager-mall.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Skapa miljöer från mallar i Azure-portalen

När du har lagt till en Azure Resource Manager-mall i labbet kan dina labbanvändare skapa miljöer i Azure-portalen genom att följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Alla tjänster**och välj sedan **DevTest Labs** i listan.

1. Välj det labb du vill använda i listan över labb.

1. På labbets sida väljer du **Lägg till**.

1. I fönstret **Välj en bas** visas de basavbildningar du kan använda, med Azure Resource Manager-mallarna listade först. Välj den Azure Resource Manager-mall du vill ha.

   ![Välja en bas](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. Ange ett **miljönamnsvärde** som ska visas för miljöanvändare i fönstret **Lägg** till.

   Azure Resource Manager-mallen definierar resten av indatafälten. Om filen *azuredeploy.parameter.json* definierar standardvärden visas dessa värden i indatafälten.

   För parametrar av typen *säker sträng*kan du använda hemligheter från ditt Azure Key Vault. Mer information om hur du lagrar hemligheter i ett nyckelvalv och använder dem när du skapar labbresurser finns [i Lagra hemligheter i Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

   ![Fönstret Lägg till](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Följande parametervärden visas inte i indatafälten, även om mallen anger dem. I stället visar formuläret tomma indatafält där labbanvändare måste ange värden när de skapar miljön.
   >
   > - GEN-UNIK
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-NYCKEL
   > - GEN-LÖSENORD

1. Välj **Lägg till** för att skapa miljön.

   Miljön börjar etableras omedelbart, med statusen visas i listan **Mina virtuella datorer.** Labbet skapar automatiskt en ny resursgrupp för att etablera alla resurser som definierats i Azure Resource Manager-mallen.

1. När miljön har skapats väljer du miljön i listan **Mina virtuella datorer** för att öppna resursgruppsfönstret och bläddra bland alla resurser som miljön har etablerat.

   ![Miljöresurser](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   Du kan också expandera miljön för att bara visa listan över virtuella datorer som miljön har etablerats.

   ![Listan Mina virtuella datorer](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Välj någon av de miljöer som ska visas för de tillgängliga åtgärderna, till exempel tillämpa artefakter, koppla datadiskar, ändra tid för automatisk avstängning med mera.

   ![Miljöåtgärder](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatisera miljöskapande med PowerShell

Det är möjligt att använda Azure-portalen för att lägga till en enda miljö i ett labb, men när ett utvecklings- eller testscenario måste skapa flera miljöer är automatiserad distribution en bättre upplevelse.

Innan du fortsätter kontrollerar du att du har en Azure Resource Manager-mall som definierar resurserna att skapa. [Lägg till och konfigurera mallen i en Git-databas](#configure-your-own-template-repositories)och [lägg till databasen i labbet](#add-template-repositories-to-the-lab).

Följande exempelskript skapar en miljö i labbet. Kommentarerna hjälper dig att förstå skriptet bättre.

1. Spara följande exempel på PowerShell-skript på hårddisken som *deployenv.ps1*.

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. Kör skriptet på följande sätt med hjälp av dina specifika värden för SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (mapp i Git-repoen) och EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Du kan också använda Azure CLI för att distribuera resurser med Resource Manager-mallar. Mer information finns i [Distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

> [!NOTE]
> Endast en användare med labbägarebehörigheter kan skapa virtuella datorer från en Resource Manager-mall med hjälp av Azure PowerShell. Om du vill automatisera skapandet av virtuella datorer med hjälp av en Resource Manager-mall och du bara har användarbehörighet kan du använda kommandot CLI-kommandot [az lab vm create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Begränsningar av Resource Manager-mallen i DevTest Labs

Beakta dessa begränsningar när du använder Resource Manager-mallar i DevTest Labs:

- Resource Manager-mallar kan inte referera till de flesta befintliga resurser. De kan bara skapa nya resurser. Om du har Resource Manager-mallar som du använder utanför DevTest Labs som refererar till befintliga resurser kan du inte använda dem i DevTest Labs. Det enda undantaget är att du kan referera till ett befintligt virtuellt nätverk.

- Du kan inte skapa formler eller anpassade avbildningar från virtuella labb-datorer som har skapats från en Resource Manager-mall.

- De flesta principer utvärderas inte när du distribuerar Resource Manager-mallar.

  Du kan till exempel ha en labbprincip som en användare bara kan skapa fem virtuella datorer. En användare kan dock distribuera en Resource Manager-mall som skapar dussintals virtuella datorer. Principer som inte utvärderas är:

  - Antal virtuella datorer per användare

  - Antal virtuella premium-datorer per labbanvändare

  - Antal premiumdiskar per labbanvändare

## <a name="next-steps"></a>Nästa steg
- När du har skapat en virtuell dator kan du ansluta till den virtuella datorn genom att välja **Anslut** i hanteringsfönstret för den virtuella datorn.
- Visa och hantera resurser i en miljö genom att välja miljö i listan **Mina virtuella datorer** i labbet.
- Utforska [Azure Resource Manager-mallarna från azure Quickstart-mallgalleriet](https://github.com/Azure/azure-quickstart-templates).
