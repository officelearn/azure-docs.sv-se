---
title: Integrera Azure DevTest Labs i dina Azure Pipelines
description: Lär dig hur du integrerar Azure DevTest Labs i din Azure Pipelines kontinuerlig integrering och leveranspipeline
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9604da5252254120ac7bd3fca3f0cc97324aef92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293223"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Integrera Azure DevTest Labs i din Azure Pipelines CI/CD-pipeline

Du kan använda tillägget *Azure DevTest Labs Tasks* för att integrera dina Azure Pipelines kontinuerlig integrering och kontinuerlig leverans (CI/CD) bygga och släppa pipelines med Azure DevTest Labs. Tillägget installerar flera uppgifter, bland annat: 

- Skapa en virtuell dator (VM)
- Skapa en anpassad avbildning från en virtuell dator
- Ta bort en virtuell dator 

Dessa uppgifter gör det enkelt att till exempel snabbt distribuera en *gyllene avbildnings-VM* för en viss testaktivitet och sedan ta bort den virtuella datorn när testet är klart.

Den här artikeln visar hur du använder Azure DevTest Labs-uppgifter för att skapa och distribuera en virtuell dator, skapa en anpassad avbildning och sedan ta bort den virtuella datorn, allt som en versionspipeline. Du utför vanligtvis uppgifterna individuellt i dina egna anpassade bygg-, test- och distributionspipelines.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Krav

- Registrera eller logga in på din [Azure DevOps-organisation](https://dev.azure.com) och [skapa ett projekt](/vsts/organizations/projects/create-project) i organisationen. 
  
- Installera tillägget Azure DevTest Labs-uppgifter från Visual Studio Marketplace.
  
  1. Gå till [Azure DevTest Labs-uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. Välj **Hämta det gratis**.
  1. Välj din Azure DevOps-organisation i listrutan och välj **Installera**. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Skapa mallen för att skapa en virtuell Azure-dator 

I det här avsnittet beskrivs hur du skapar Azure Resource Manager-mallen som du använder för att skapa en Azure-virtuell dator på begäran.

1. Om du vill skapa en Resource Manager-mall i prenumerationen följer du proceduren i [Använd en Resource Manager-mall](devtest-lab-use-resource-manager-template.md).
   
1. Innan du skapar Resource Manager-mallen lägger du till [WinRM-artefakten](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) som en del av skapandet av den virtuella datorn. Distributionsuppgifter som *Azure File Copy* och *PowerShell på måldatorer* behöver WinRM-åtkomst. WinRM-artefakten kräver ett värdnamn som en parameter, som bör vara det fullständigt kvalificerade domännamnet (FQDN) för den virtuella datorn. 
   
   > [!NOTE]
   > När du använder WinRM med en delad IP-adress måste du lägga till en NAT-regel för att mappa en extern port till WinRM-porten. Du behöver inte NAT-regeln om du skapar den virtuella datorn med en offentlig IP-adress.
   
   
1. Spara mallen på datorn som en fil med namnet *CreateVMTemplate.json*.
   
1. Checka in mallen till källkontrollsystemet.

## <a name="create-a-script-to-get-vm-properties"></a>Skapa ett skript för att hämta vm-egenskaper

När du kör aktivitetssteg som *Azure File Copy* eller *PowerShell på måldatorer* i versionspipelinen samlar följande skript in de värden som du behöver för att distribuera en app till en virtuell dator. Du skulle vanligtvis använda dessa uppgifter för att distribuera din app till en Virtuell Azure. Aktiviteterna kräver värden som vm-resursgruppsnamn, IP-adress och FQDN.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Så här skapar du skriptfilen:

1. Öppna en textredigerare och klistra in följande skript i den.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Spara filen med ett namn som *GetLabVMParams.ps1*och checka in den i källkontrollsystemet. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Skapa en versionspipeline i Azure Pipelines

Så här skapar du en ny versionspipeline:

1. Välj **Pipelines-versioner** > **Releases** från vänster navigering på din Azure DevOps-projektsida.
1. Välj **Ny pipeline**.
1. Under **Välj en mall**bläddrar du nedåt och väljer Tomt **jobb**och väljer sedan **Använd**.

### <a name="add-and-set-variables"></a>Lägg till och ange variabler

Pipeline-uppgifterna använder de värden som du har tilldelat den virtuella datorn när du skapade Resource Manager-mallen i Azure-portalen. 

Så här lägger du till variabler för värdena: 

1. Välj fliken **Variabler** på pipelinesidan.
   
1. För varje variabel väljer du **Lägg till** och anger namn och värde:
   
   |Namn|Värde|
   |---|---|
   |*vmName (vmName)*|VM-namn som du har tilldelat i resource manager-mallen|
   |*Användarnamn*|Användarnamn för att komma åt den virtuella datorn|
   |*lösenord*|Lösenord för användarnamnet. Välj låsikonen för att dölja och skydda lösenordet.

### <a name="create-a-devtest-labs-vm"></a>Skapa en VM-dator för DevTest Labs

Nästa steg är att skapa den gyllene avbildningen VM att använda för framtida distributioner. Du skapar den virtuella datorn i din Azure DevTest Labs-instans med hjälp av Azure *DevTest Labs Create VM-uppgift.*

1. På fliken Pipeline **pipeline** på fliken Pipeline markerar du den hyperlänkade texten i **+** steg **1** för att visa **stegaktiviteter**och väljer sedan plustecknet bredvid **Agent-jobb**. 
   
1. Under **Lägg till uppgifter**väljer du Azure **DevTest Labs Create VM**och väljer Lägg **till**. 
   
1. Välj **Skapa Azure DevTest Labs VM** i den vänstra rutan. 

1. I den högra rutan fyller du i formuläret enligt följande:
   
   |Field|Värde|
   |---|---|
   |**Azure RM-prenumeration**|Välj en tjänstanslutning eller prenumeration från **Tillgängliga Azure-tjänstanslutningar** eller **tillgängliga Azure-prenumerationer** i listrutan och välj **Auktorisera** om det behövs.<br /><br />**Anm.:** Information om hur du skapar en mer begränsad behörighetsanslutning till din Azure-prenumeration finns i Slutpunkten för [Azure Resource Manager-tjänst](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).|
   |**Lab Namn**|Välj namnet på ett befintligt labb där labbet VM ska skapas.|
   |**Mallnamn**|Ange den fullständiga sökvägen och namnet på den mallfil som du sparade i källkodsdatabasen. Du kan använda inbyggda egenskaper för att förenkla sökvägen, till exempel:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Mallparametrar**|Ange parametrarna för de variabler som du definierade tidigare:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Utdatavariabler** > **Lab VM-ID**|Ange variabeln för det skapade labbet VM ID. Om du använder **standardlabbetVMId**kan du referera till variabeln i efterföljande uppgifter som *$(labVMId)*.<br /><br />Du kan skapa ett annat namn än standard, men kom ihåg att använda rätt namn i efterföljande aktiviteter. Du kan skriva labb-VM-ID:t i följande formulär:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>Samla in information om vm-datorn devtest labs

Kör skriptet som du skapade tidigare för att samla in information om den virtuella datorn för DevTest Labs. 

1. På fliken Pipeline **pipeline** på fliken Pipeline markerar du den hyperlänkade texten i **+** steg **1** för att visa **stegaktiviteter**och väljer sedan plustecknet bredvid **Agent-jobb**. 
   
1. Under **Lägg till uppgifter**väljer du Azure **PowerShell**och väljer **Lägg till**. 
   
1. Välj **Azure PowerShell-skript: FilePath** i den vänstra rutan. 
   
1. I den högra rutan fyller du i formuläret enligt följande:
   
   |Field|Värde|
   |---|---|
   |**Typ av Azure-anslutning**|Välj **Azure Resource Manager**.|
   |**Azure-prenumeration**|Välj din tjänstanslutning eller prenumeration.| 
   |**Skripttyp**|Välj **sökväg till skriptfil**.|
   |**Skriptsökväg**|Ange den fullständiga sökvägen och namnet på Det PowerShell-skript som du sparade i källkodsdatabasen. Du kan använda inbyggda egenskaper för att förenkla sökvägen, till exempel:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Skriptargument**|Ange namnet på *labVmId-variabeln* som har fyllts i av föregående uppgift, till exempel:<br /><br />`-labVmId '$(labVMId)'`|

Skriptet samlar in de värden som krävs och lagrar dem i miljövariabler i versionspipelinen, så att du enkelt kan referera till dem i efterföljande steg.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Skapa en VM-avbildning från vm-datorn DevTest Labs

Nästa uppgift är att skapa en avbildning av den nyligen distribuerade virtuella datorn i din Azure DevTest Labs-instans. Du kan sedan använda avbildningen för att skapa kopior av den virtuella datorn på begäran när du vill köra en utvecklingsaktivitet eller köra vissa tester. 

1. På fliken Pipeline **pipeline** på fliken Pipeline markerar du den hyperlänkade texten i **+** steg **1** för att visa **stegaktiviteter**och väljer sedan plustecknet bredvid **Agent-jobb**. 
   
1. Under **Lägg till uppgifter**väljer du Azure **DevTest Labs Skapa anpassad avbildning**och väljer **Lägg till**. 
   
1. Konfigurera uppgiften enligt följande:
   
   |Field|Värde|
   |---|---|
   |**Azure RM-prenumeration**|Välj din tjänstanslutning eller prenumeration.|
   |**Lab Namn**|Välj namnet på ett befintligt labb där avbildningen ska skapas.|
   |**Anpassat bildnamn**|Ange ett namn på den anpassade bilden.|
   |**Beskrivning** (valfritt)|Ange en beskrivning för att göra det enkelt att välja rätt bild senare.|
   |**Source Lab VM** > **VM-ID för källa för** källa för källa|Om du har ändrat standardnamnet för LabVMId-variabeln anger du det här. Standardvärdet är **$(labVMId)**.|
   |**Utdatavariabler** > **anpassat bild-ID**|Du kan redigera standardnamnet på variabeln om det behövs.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Distribuera din app till vm-datorn devtest labs (valfritt)

Du kan lägga till uppgifter för att distribuera din app till den nya virtuella datorn för DevTest Labs. De uppgifter som du vanligtvis använder för att distribuera appen är *Azure File Copy* och *PowerShell på måldatorer*.

Den VM-information som du behöver för parametrarna för dessa uppgifter lagras i tre konfigurationsvariabler med namnet **labVmRgName**, **labVMIpAddress**och **labVMFqdn** i versionspipelinen. Om du bara vill experimentera med att skapa en Vm-dator för DevTest Labs och en anpassad avbildning, utan att distribuera en app till den, kan du hoppa över det här steget.

### <a name="delete-the-vm"></a>Ta bort den virtuella datorn

Den sista uppgiften är att ta bort den virtuella datorn som du distribuerade i din Azure DevTest Labs-instans. Du tar normalt bort den virtuella datorn när du har kört de utgående uppgifterna eller kört de tester som du behöver på den distribuerade virtuella datorn. 

1. På fliken Pipeline **pipeline** på fliken Pipeline markerar du den hyperlänkade texten i **+** steg **1** för att visa **stegaktiviteter**och väljer sedan plustecknet bredvid **Agent-jobb**. 
   
1. Under **Lägg till uppgifter**väljer du Ta bort virtuell dator för Azure **DevTest Labs**och välj Lägg **till**. 
   
1. Konfigurera uppgiften enligt följande:
   
   - Under **Azure RM-prenumeration**väljer du din tjänstanslutning eller prenumeration. 
   - Ange det här om du har ändrat standardnamnet för LabVMId-variabeln för Lab **VM-ID.** Standardvärdet är **$(labVMId)**.
   
### <a name="save-the-release-pipeline"></a>Spara versionspipelinen

Så här sparar du den nya versionspipelinen:

1. Välj namnet **Ny versionspipeline** på utgivningspipelinens sida och ange ett nytt namn för pipelinen. 
   
1. Välj ikonen **Spara** längst upp till höger. 

## <a name="create-and-run-a-release"></a>Skapa och kör en version

Så här skapar och kör du en version med den nya pipelinen:

1. Välj **Skapa version** längst upp till höger på utgivningspipelinesidan. 
   
1. Under **Artefakter**väljer du den senaste versionen och väljer sedan **Skapa**.
   
1. Vid varje utgivningssteg uppdaterar du vyn för din DevTest Labs-instans i Azure-portalen för att visa skapande av virtuella datorer, skapande av avbildningar och borttagning av virtuella datorer.

Du kan använda den anpassade avbildningen för att skapa virtuella datorer när du behöver dem.

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [skapar miljöer med flera virtuella datorer med Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
- Utforska fler snabbstarts resource manager-mallar för DevTest Labs automatisering från den [offentliga DevTest Labs GitHub repo](https://github.com/Azure/azure-quickstart-templates).
- Om det behövs läser du [felsökningssidan för Azure DevOps.](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting)
 
