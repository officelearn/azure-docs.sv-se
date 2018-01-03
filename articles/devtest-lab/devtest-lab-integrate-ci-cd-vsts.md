---
title: Integrera Azure DevTest Labs i din kontinuerlig integration och leverans pipeline i VSTS | Microsoft Docs
description: "Lär dig att integrera Azure DevTest Labs i din VSTS kontinuerlig integrering och leverans pipeline"
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: tarcher
ms.openlocfilehash: 3cd6939e890f59e7c5b188edd8620da5c9fcc935
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2017
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>Integrera Azure DevTest Labs i din VSTS kontinuerlig integrering och leverans pipeline
Du kan använda den **Azure DevTest Labs uppgifter tillägget** installerad i Visual Studio Team Services (VSTS) att enkelt integrera CI/CD-version och utgåva rörledningen med Azure DevTest Labs. Tillägget installeras tre åtgärder för att skapa en virtuell dator, skapa en anpassad avbildning från en virtuell dator och ta bort en virtuell dator. Den här processen gör det enkelt att exempelvis snabbt distribuera en ”gyllene bild” för specifika test aktivitet och sedan ta bort när testet är klart.

Den här artikeln visar hur du skapar och distribuerar en virtuell dator, skapa en anpassad avbildning och ta sedan bort den virtuella datorn, alla som en fullständig pipeline. Vanligtvis, men använder du aktiviteter individuellt i din egen anpassade version test distribuerar pipeline.

## <a name="before-you-begin"></a>Innan du börjar
Innan du kan integrera din CI/CD-pipeline med Azure DevTest Labs, måste du först installera tillägget från Visual Studio Marketplace.
1. Gå till [Azure DevTest Labs uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)
1. Välj installation
1. Slutför guiden

## <a name="create-a-resource-manager-template"></a>Skapa en Resource Manager-mall
Det här avsnittet beskrivs hur du skapar Azure Resource Manager-mall som används för att skapa en virtuell dator i Azure på begäran.

1. Följ stegen i [använder en Resource Manager-mall](devtest-lab-use-resource-manager-template.md) du skapar en Resource Manager-mall i din prenumeration.
1. Lägg till den [WinRM artefakt](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) som en del av att skapa den virtuella datorn (innan du genererar Resource Manager-mallen).

   WinRM-åtkomst krävs för att använda distribuera aktiviteter som **Azure filkopiering** och **PowerShell på måldatorerna**.

   > [!NOTE]
   > När du använder WinRM med delade IP-adress, måste du lägga till en NAT-regel för att mappa en extern port för WinRM-port. Detta är inte nödvändigt om den virtuella datorn skapas med en offentlig IP-adress.
   >
   >

1. Spara mallen som en fil på din dator. Namn på filen **CreateVMTemplate.json**.
1. Kontrollera mallen i ditt källkontrollsystem.
1. Öppna en textredigerare och kopiera följande skript till den.

   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzureRmResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzureRmResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzureRmResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Kontrollera om det i ditt källkontrollsystem. Ge den namnet något som liknar **GetLabVMParams.ps1**.

   Det här skriptet när det körs på agenten som en del av definitionen versionen samlar in värden som behövs för att distribuera din app till den virtuella datorn om du använder aktiviteten åtgärder som **Azure filkopiering** eller **PowerShell på måldatorerna**. Du använder vanligtvis dessa uppgifter för att distribuera appar till en virtuell dator i Azure och de kräver att värden som VM resursgruppens namn, IP-adress och fullständigt kvalificerat domännamn (FDQN).

## <a name="create-the-release-definition-in-release-management"></a>Skapa en definition för versionen i släpper hantering
Utför stegen nedan för att skapa en definition för versionen.

1. Öppna den **versioner** för den **Skapa & släpper** hubb och välj den ”**+**” ikon.
1. I den **skapa versionen definition** markerar den **tom** mall, och välj **nästa**.
1. På nästa sida väljer **välja senare** och välj sedan **skapa** att skapa en ny version definition med en standard-miljön och inga länkade artefakter.
1. I den nya versionen definition, Välj ellipserna (...) bredvid namnet på miljön för att öppna snabbmenyn och välj **konfigurera variabler**. 
1. I den **konfigurera - miljö** dialogrutan, ange följande värden för variabler som du använder i versionen definition uppgifter:
   - **vmName**: Ange namnet du tilldelade till den virtuella datorn när du skapade Resource Manager-mall i Azure-portalen.
   - **Användarnamnet**: Ange användarnamnet som du har tilldelat till den virtuella datorn när du har skapat Resource Manager-mall i Azure-portalen.
   - **lösenordet**: Ange lösenordet som du tilldelade till den virtuella datorn när du skapade Resource Manager-mall i Azure-portalen. Använd ”hänglåsikon” för att dölja och säkra lösenord.

   <a name="create-a-vm"></a>Skapa en virtuell dator
   ---

   Det första steget i den här distributionen är att skapa den virtuella datorn ska användas som ”gyllene bild” för efterföljande distributioner. Du kan skapa den virtuella datorn i din Azure DevTest Lab-instans med hjälp av aktiviteten som utvecklats speciellt för detta ändamål. I version-definition väljer **+ Lägg till aktiviteter** och lägga till en **Skapa virtuell dator i Azure DevTest Labs** aktivitet från fliken distribuera. Konfigurera enligt följande:

   Se [Azure DevTest Labs uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) att skapa den virtuella datorn ska användas för efterföljande distributioner.
   - **Azure RM-prenumeration**: Välj en anslutning i listan under **tillgängliga Azure Service anslutningar** eller skapa en mer begränsad behörighet för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager tjänstslutpunkten](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
   - **Labbnamnet**: Välj namnet på den instans som du skapade tidigare.
   - **Mallnamnet**: Ange den fullständiga sökvägen och namnet på den mallfil som du sparade i din kod källdatabasen. Du kan använda inbyggda egenskaperna för versionshantering för att förenkla sökväg, till exempel:
      ```
      $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
      ```
   - **Mallparametrar**: Ange parametrarna för variabler som definieras i mallen. Använd namnen på de variabler som du har definierat i miljön, till exempel:
      ```
      -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
      ```
   - **Utdata variabler - labb VM ID**: du behöver ID för den nya virtuella datorn för efterföljande steg. Standardnamnet för miljövariabeln som fylls automatiskt med detta ID anges i den **Utdatavariabler** avsnitt. Du kan redigera variabel om det behövs, men kom ihåg att använda rätt namn i efterföljande aktiviteter. Lab VM-ID är i formatet:
      ```
      /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
      ```
1. Kör skriptet som du skapade tidigare för att samla in information om DevTest Labs VM. I version-definition väljer **+ Lägg till aktiviteter** och lägga till en **Azure PowerShell** aktiviteten från den **distribuera** fliken. Konfigurera aktiviteten enligt följande:

   Se [distribuera: Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) och kör skriptet för att samla in information om DevTest Labs VM.

   - **Azure anslutningstypen**: Azure Resource Manager.
   - **Azure RM-prenumeration**: Välj en anslutning i listan under **tillgängliga Azure Service anslutningar** eller skapa en mer begränsad behörighet för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager tjänstslutpunkten](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
   - **Skripttyp**: skriptfil.
   - **Script sökvägen**: Ange den fullständiga sökvägen och namnet på det skript som du sparade i din kod källdatabasen. Du kan använda inbyggda egenskaperna för versionshantering för att förenkla sökväg, till exempel:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   - **Script argument**: Ange namnet på miljövariabel som var fylls automatiskt med labbet VM-ID för den föregående aktiviteten till exempel som argumentet skript: 
      ```
      -labVmId '$(labVMId)'
      ```
   Skriptet samlar in de efterfrågade värdena och lagrar dem i miljövariabler i definitionen för versionen så att du lätt kan se dem i efterföljande aktivitet steg.

1. Du kan nu distribuera din app till den nya DevTest Labs VM. Uppgifter som du vanligtvis använder för att distribuera är **Azure filkopiering** och **PowerShell på måldatorerna**.
   - Information om den virtuella datorn som du behöver för parametrarna för dessa uppgifter är lagrad i tre configuration variabler med namnet **labVmRgName**, **labVMIpAddress**, och **labVMFqdn**i definitionen för versionen.
   - Om du vill experimentera med att skapa ett DevTest Labs VM och en anpassad avbildning utan att distribuera en app till den, kan du hoppa över det här steget.

   <a name="create-an-image"></a>Skapa en avbildning
   ---

   Nästa steg är att skapa en avbildning av den nyligen distribuerade virtuella datorn i Azure DevTest Labs-instans. Du kan sedan använda den här avbildningen för att skapa kopior av den virtuella datorn på begäran, när du vill köra en aktivitet som dev eller köra vissa tester. I version-definition väljer **+ Lägg till aktiviteter** och lägga till en **Azure DevTest Labs Skapa anpassad bild** aktiviteten från den **distribuera** fliken. Konfigurera enligt följande:

   Se [Azure DevTest Labs uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) att skapa avbildningen.
   - **Azure RM-prenumeration**: Välj en anslutning i listan under **tillgängliga Azure Service anslutningar** eller skapa en mer begränsad behörighet för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager tjänstslutpunkten](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
   - **Labbnamnet**: Välj namnet på den instans som du skapade tidigare.
   - **Anpassade avbildningsnamn**: Ange ett namn för den anpassade avbildningen.
   - **Beskrivning**: du kan också ange en beskrivning som gör det lättare att välja rätt avbildning senare.
   - **Datakällan Lab VM - ID för meddelandekälla Lab VM**: Om du har ändrat standardnamnet för miljövariabeln som var fylls automatiskt med ID labbet VM av en tidigare aktivitet kan redigera den här. Standardvärdet är *$(labVMId)*.
   - **Utdata variabler - ID för anpassad bild**: du behöver ID för den nyligen skapade avbildningen när du vill hantera eller ta bort den. Standardnamnet för miljövariabeln som fylls automatiskt med detta ID anges i den **Utdatavariabler** avsnitt. Du kan redigera variabeln om det behövs.
   
   <a name="delete-the-vm"></a>Ta bort den virtuella datorn
   ---
   
   Det sista steget i det här exemplet är att ta bort den virtuella datorn som du distribuerade i Azure DevTest Labs-instans. Normalt du ta bort den virtuella datorn när du kör dev uppgifterna eller köra testerna måste på den distribuerade virtuella datorn. I version-definition väljer **+ Lägg till aktiviteter** och lägga till en **ta bort virtuell dator i Azure DevTest Labs** aktiviteten från den **distribuera** fliken. Konfigurera enligt följande:

   Se [Azure DevTest Labs uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) att ta bort den virtuella datorn.
   - **Azure RM-prenumeration**: Välj en anslutning i listan under **tillgängliga Azure Service anslutningar** eller skapa en mer begränsad behörighet för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager tjänstslutpunkten](https://docs.microsoft.com/en-us/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
   - **VM-ID för labbet**: Om du har ändrat standardnamnet för miljövariabeln som var fylls automatiskt med ID labbet VM av en tidigare aktivitet kan redigera den här. Standardvärdet är *$(labVMId)*.
1. Ange ett namn för versionen definitionen och spara den.
1. Skapa en ny version, väljer du den senaste versionen och distribuera den till den enda miljön i definitionen.

Uppdatera vyn i din instans DevTest Labs i Azure portal och se den virtuella datorn och bild skapas och den virtuella datorn tas bort igen i varje steg.

Du kan nu använda den anpassade avbildningen för att skapa virtuella datorer vid behov.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapa flera Virtuella miljöer med Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
* Utforska mer Resource Manager-snabbstartsmallar för DevTest Labs automation från den [offentliga DevTest Labs GitHub-repo-](https://github.com/Azure/azure-quickstart-templates).
* Om det behövs, finns det [VSTS felsökning](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting) sidan.
