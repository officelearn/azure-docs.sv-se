---
title: Integrera Azure DevTest Labs i din kontinuerlig integrering och leverans pipeline i Azure Pipelines | Microsoft Docs
description: Lär dig hur du integrerar Azure DevTest Labs i din Azure-Pipelines för kontinuerlig integrering och leverans pipeline
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 459b06df954d9cc913b6d1503c9f876f93b494e9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082959"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-devops-continuous-integration-and-delivery-pipeline"></a>Integrera Azure DevTest Labs i din Azure DevOps kontinuerlig integrering och leverans pipeline
Du kan använda den *Azure DevTest Labs-uppgifter* som är installerad i Azure DevOps att enkelt integrera din version och versionen CI/CD-pipeline med Azure DevTest Labs. Tillägget installeras tre uppgifter: 
* Skapa en virtuell dator
* Skapa en anpassad avbildning från en virtuell dator
* Ta bort en virtuell dator 

Processen gör det enkelt att snabbt distribuera exempelvis ett ”gyllene bild” för ett specifikt test-aktivitet och tar bort den när testet är klart.

Den här artikeln visar hur du skapar och distribuerar en virtuell dator, skapa en anpassad avbildning och ta sedan bort den virtuella datorn och alla som en fullständig pipeline. Normalt utför varje aktivitet individuellt i din egen anpassade build-test-distribuera pipeline:

## <a name="before-you-begin"></a>Innan du börjar
Innan du kan integrera dina CI/CD-pipeline med Azure DevTest Labs, måste du installera tillägget från Visual Studio Marketplace.
1. Gå till [Azure DevTest Labs-uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Välj **Installera**.
1. Slutför guiden.

## <a name="create-a-resource-manager-template"></a>Skapa en Azure Resource Manager-mall
Det här avsnittet beskriver hur du skapar Azure Resource Manager-mallen som används för att skapa en Azure virtuell dator på begäran.

1. Om du vill skapa en Resource Manager-mall i din prenumeration, slutför du registreringen i [använda Resource Manager-mall](devtest-lab-use-resource-manager-template.md).
1. Innan du skapar Resource Manager-mall lägger du till den [WinRM artefakt](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) som en del av den virtuella datorn.

   WinRM-åtkomst krävs för att använda distributionsåtgärder som *Azure File Copy* och *PowerShell på måldatorer*.

   > [!NOTE]
   > När du använder WinRM med delade IP-adress, måste du lägga till en NAT-regel för att mappa en extern port till WinRM-port. Det här steget krävs inte om du skapar den virtuella datorn med en offentlig IP-adress.
   >
   >

1. Spara mallen som en fil på din dator. Ge filen namnet **CreateVMTemplate.json**.
1. Kontrollera mallen till ditt källkontrollsystem.
1. Öppna en textredigerare och klistra in följande skript i den.

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

1. Kontrollera skriptet ditt källkontrollsystem. Något som **GetLabVMParams.ps1**.

   När du kör det här skriptet på agenten som en del av versionspipelinen, och om du använder uppgift som *Azure File Copy* eller *PowerShell på måldatorer*, skriptet samlar in de värden som du behöver distribuera din app till den virtuella datorn. Du skulle normalt använda dessa uppgifter för att distribuera appar till en Azure-dator. Uppgifterna kan du kräva värden som namn på resursgrupp för virtuell dator, IP-adress och fullständigt kvalificerade domännamnet (fullständigt domännamn).

## <a name="create-a-release-pipeline-in-release-management"></a>Skapa en pipeline för versionen i Release Management
Om du vill skapa versionspipelinen, gör du följande:

1. På den **versioner** fliken den **Build & Release** hubb, klicka på plustecknet (+).
1. I den **skapa versionsdefinition** väljer den **tom** mallen och välj sedan **nästa**.
1. Välj **väljer senare**, och välj sedan **skapa** att skapa en ny version-pipeline med en standardmiljö och inga länkade artefakter.
1. Välj ellipsen (...) bredvid miljönamnet på för att öppna snabbmenyn i nya versionspipelinen, och välj sedan **konfigurera variabler**. 
1. I den **konfigurera - miljö** fönster för de variabler som du använder i pipeline publiceringsuppgifter anger du följande värden:

   a. För **vmName**, anger du namnet som tilldelats den virtuella datorn när du skapade Resource Manager-mall i Azure-portalen.

   b. För **användarnamn**, anger du användarnamnet som du tilldelade till den virtuella datorn när du skapade Resource Manager-mall i Azure-portalen.

   c. För **lösenord**, ange lösenordet som du tilldelade till den virtuella datorn när du skapade Resource Manager-mall i Azure-portalen. Använd hänglåsikonen ”” för att dölja och skydda lösenordet.

### <a name="create-a-vm"></a>Skapa en virtuell dator

Nästa steg i distributionen är att skapa den virtuella datorn ska användas som ”gyllene bild” för efterföljande distributioner. Du kan skapa den virtuella datorn i Azure DevTest Lab-instans med hjälp av den aktivitet som har utvecklats särskilt för detta ändamål. 

1. Välj i versionspipelinen, **lägga till aktiviteter**.
1. På den **distribuera** fliken, lägga till en *Skapa virtuell dator i Azure DevTest Labs* uppgift. Konfigurera aktiviteten enligt följande:

   > [!NOTE]
   > Om du vill skapa den virtuella datorn ska användas för efterföljande distributioner [Azure DevTest Labs-uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. För **Azure RM-prenumeration**, Välj ett projekt i den **tillgängliga Azure-Tjänstanslutningar** , eller skapa en mer begränsade behörigheter för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager-tjänstslutpunkt](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   b. För **Labbnamn**, Välj namnet på den instans som du skapade tidigare.

   c. För **mallnamn**, ange den fullständiga sökvägen och namnet på den mallfil som du sparade till lagringsplatsen för källkoden. Du kan använda inbyggda egenskaperna för versionshantering för att förenkla sökväg, till exempel:

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. För **mallparametrarna**, ange parametrar för variabler som definieras i mallen. Använda namnen på de variabler som du har definierat i miljön, till exempel:

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. För **utdata variabler - ID för virtuell dator i labbet**, du behöver ID för den nya virtuella datorn för efterföljande steg. Du ställer in standardnamnet för miljövariabeln som fylls i automatiskt med detta ID i den **utdata variabler** avsnittet. Du kan redigera variabeln om det behövs, men kom ihåg att använda rätt namn i efterföljande aktiviteter. Den Virtuella labb-ID är skriven i följande format:

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

1. Kör skriptet som du skapade tidigare för att samla in information om DevTest Labs-VM. 
1. I versionspipelinen, väljer **lägga till aktiviteter** och klicka sedan på den **distribuera** fliken, lägga till en *Azure PowerShell* uppgift. Konfigurera aktiviteten enligt följande:

   > [!NOTE]
   > Om du vill samla in information om DevTest Labs VM, se [distribuera: Azure PowerShell](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzurePowerShellV3) och kör skriptet.

   a. För **Azure anslutningstypen**väljer **Azure Resource Manager**.

   b. För **Azure RM-prenumeration**, Välj en anslutning i listan under **tillgängliga Azure-Tjänstanslutningar**, eller skapa en mer begränsade behörigheter för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager-tjänstslutpunkt](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   c. För **Skripttyp**väljer **skriptfilen**.
 
   d. För **skriptets sökväg**, ange den fullständiga sökvägen och namnet på det skript som du sparade till lagringsplatsen för källkoden. Du kan använda inbyggda egenskaperna för versionshantering för att förenkla sökväg, till exempel:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. För **skriptargument**, anger du namnet på miljövariabeln som har fylls automatiskt med lab VM-ID för den föregående aktiviteten, till exempel: 
      ```
      -labVmId '$(labVMId)'
      ```
    Skriptet samlar in värdena som krävs och lagrar dem i miljövariabler i versionspipelinen, så att du enkelt kan hänvisa till dem i efterföljande steg.

1. Distribuera din app till den nya DevTest Labs virtuella datorn. Uppgifter som du normalt använda för att distribuera appen är *Azure File Copy* och *PowerShell på måldatorer*.
   Information om den virtuella datorn som du behöver för parametrarna för uppgifterna lagras i tre configuration variabler med namnet **labVmRgName**, **labVMIpAddress**, och **labVMFqdn**inom versionspipelinen. Om du bara vill experimentera med att skapa en DevTest Labs virtuell dator och en anpassad avbildning, utan att distribuera en app kan du hoppa över det här steget.

### <a name="create-an-image"></a>Skapa en avbildning

Nästa steg är att skapa en avbildning av den nyligen distribuerade virtuella datorn i din Azure DevTest Labs-instans. Du kan sedan använda avbildningen för att skapa kopior av den virtuella datorn på begäran när du vill köra en dev-uppgift eller köra vissa tester. 

1. Välj i versionspipelinen, **lägga till aktiviteter**.
1. På den **distribuera** fliken, lägga till en **Azure DevTest Labs Skapa anpassad avbildning** uppgift. Konfigurera enligt följande:

   > [!NOTE]
   > För att skapa avbildningen, se [Azure DevTest Labs-uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. För **Azure RM-prenumeration**i den **tillgängliga Azure-Tjänstanslutningar** listan, Välj en anslutning i listan eller skapa en mer begränsade behörigheter för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager-tjänstslutpunkt](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   b. För **Labbnamn**, Välj namnet på den instans som du skapade tidigare.

   c. För **anpassade avbildningsnamn**, ange ett namn för den anpassade avbildningen.

   d. (Valfritt) För **beskrivning**, ange en beskrivning som gör det enkelt att välja rätt avbildning senare.

   e. För **Virtuella labb - VM-ID för meddelandekälla Lab**, om du har ändrat standardnamn för miljövariabeln som automatiskt har fyllts med ID för Virtuella labbdatorer med en tidigare uppgift, redigera den här. Standardvärdet är **$(labVMId)**.

   f. För **utdata variabler - ID för anpassad bild**, du behöver ID för den nyligen skapade avbildningen när du vill hantera eller ta bort den. Standardnamnet för miljövariabeln som fylls i automatiskt med detta ID anges i den **utdata variabler** avsnittet. Du kan redigera variabeln om det behövs.

### <a name="delete-the-vm"></a>Ta bort den virtuella datorn

Det sista steget är att ta bort den virtuella datorn som du distribuerade i din Azure DevTest Labs-instans. Du skulle normalt tar bort den virtuella datorn när du kör uppgifterna för utveckling eller köra testerna som du behöver på den distribuerade virtuella datorn. 

1. I versionspipelinen, väljer **lägga till aktiviteter** och klicka sedan på den **distribuera** fliken, lägga till en *ta bort virtuell dator i Azure DevTest Labs* uppgift. Konfigurera enligt följande:

      > [!NOTE]
      > Om du vill ta bort den virtuella datorn, se [Azure DevTest Labs-uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. För **Azure RM-prenumeration**, Välj ett projekt i den **tillgängliga Azure-Tjänstanslutningar** , eller skapa en mer begränsade behörigheter för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager-tjänstslutpunkt](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).
 
   b. För **Lab VM-ID**, om du har ändrat standardnamn för miljövariabeln som automatiskt har fyllts med ID för Virtuella labbdatorer med en tidigare uppgift, redigera den här. Standardvärdet är **$(labVMId)**.

1. Ange ett namn för versionspipelinen och spara den.
1. Skapa en ny version, Välj den senaste versionen och distribuera den till den enda miljön i pipelinen.

Uppdatera vyn för din DevTest Labs-instans i Azure portal för att visa den virtuella datorn och avbildning som skapas och den virtuella datorn som tas bort igen i varje steg.

Du kan nu använda den anpassade avbildningen för att skapa virtuella datorer när de är obligatoriska.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapa miljöer för flera virtuella datorer med Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
* Utforska fler Snabbstart Resource Manager-mallar för DevTest Labs automation från den [offentliga DevTest Labs GitHub-lagringsplatsen](https://github.com/Azure/azure-quickstart-templates).
* Om det behövs kan du se den [felsökning för Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting) sidan.
 
