---
title: Integrera Azure DevTest Labs i din kontinuerlig integration och leverans pipeline i VSTS | Microsoft Docs
description: Lär dig att integrera Azure DevTest Labs i din VSTS kontinuerlig integrering och leverans pipeline
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
ms.openlocfilehash: 1af195e644fe93e0c59f5e4402dd8942f5fe1aba
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>Integrera Azure DevTest Labs i din VSTS kontinuerlig integrering och leverans pipeline
Du kan använda den *Azure DevTest Labs uppgifter* som är installerad i Visual Studio Team Services (VSTS) att enkelt integrera din CI/CD-version och utgåva pipeline med Azure DevTest Labs. Tillägget installeras tre uppgifter: 
* Skapa en virtuell dator
* Skapa en anpassad avbildning från en virtuell dator
* Ta bort en virtuell dator 

Processen gör det enkelt att snabbt distribuera exempelvis ett ”gyllene bild” för en specifik test-aktivitet och tar bort den när testet är klart.

Den här artikeln visar hur du skapar och distribuerar en virtuell dator, skapa en anpassad avbildning och ta bort den virtuella datorn, alla som en fullständig pipeline. Normalt utför varje aktivitet individuellt i din egen anpassade version test distribuerar pipeline:

## <a name="before-you-begin"></a>Innan du börjar
Innan du kan integrera din CI/CD-pipeline med Azure DevTest Labs, måste du installera tillägget från Visual Studio Marketplace.
1. Gå till [Azure DevTest Labs uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Välj **installera**.
1. Slutför guiden.

## <a name="create-a-resource-manager-template"></a>Skapa en Azure Resource Manager-mall
Det här avsnittet beskrivs hur du skapar Azure Resource Manager-mall som används för att skapa en virtuell Azure-dator på begäran.

1. Om du vill skapa en Resource Manager-mall i din prenumeration, slutför du proceduren i [använder en Resource Manager-mall](devtest-lab-use-resource-manager-template.md).
1. Innan du skapar Resource Manager-mall kan lägga till den [WinRM artefakt](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) som en del av att skapa den virtuella datorn.

   WinRM-åtkomst krävs för att använda distributionsåtgärder som *Azure filkopiering* och *PowerShell på måldatorerna*.

   > [!NOTE]
   > När du använder WinRM med en IP-adress måste du lägga till en NAT-regel för att mappa en extern port för WinRM-port. Det här steget är inte nödvändigt om du skapar den virtuella datorn med en offentlig IP-adress.
   >
   >

1. Spara mallen som en fil på din dator. Namn på filen **CreateVMTemplate.json**.
1. Kontrollera mallen ditt källkontrollsystem.
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

1. Kontrollera skriptet ditt källkontrollsystem. Ge den namnet ungefär **GetLabVMParams.ps1**.

   När du kör skriptet på agenten som en del av definitionen för versionen och om du använder aktiviteten åtgärder som *Azure filkopiering* eller *PowerShell på måldatorerna*, skriptet samlar in de värden som du behöver distribuera din app till den virtuella datorn. Normalt använder du dessa uppgifter kan distribuera appar till en Azure VM. Uppgifterna kräver att värden som VM resursgruppens namn, IP-adress och fullständigt kvalificerat domännamn (FDQN).

## <a name="create-a-release-definition-in-release-management"></a>Skapa en definition av versionen i släpper Management
Om du vill skapa definition för versionen, gör du följande:

1. På den **versioner** för den **Skapa & släpper** hubben, klicka på plustecknet (+).
2. I den **skapa versionen definition** väljer den **tom** mall och välj sedan **nästa**.
3. Välj **välja senare**, och välj sedan **skapa** att skapa en ny version definition med en standard-miljön och inga länkade artefakter.
4. Om du vill öppna snabbmenyn, i den nya frisläppa definition, Välj ellips (...) bredvid namnet på miljön och välj sedan **konfigurera variabler**. 
5. I den **konfigurera - miljö** för variabler som du använder i versionen definition aktiviteter, ange följande värden:

   a. För **vmName**, ange namnet som du har tilldelat till den virtuella datorn när du har skapat Resource Manager-mall i Azure-portalen.

   b. För **användarnamn**, ange ett användarnamn som tilldelats den virtuella datorn när du skapade Resource Manager-mall i Azure-portalen.

   c. För **lösenord**, ange lösenordet som du tilldelade till den virtuella datorn när du skapade Resource Manager-mall i Azure-portalen. Använd ”hänglåsikon” för att dölja och säkra lösenord.

### <a name="create-a-vm"></a>Skapa en virtuell dator

Nästa steg i distributionen är att skapa den virtuella datorn ska användas som ”gyllene bild” för efterföljande distributioner. Du kan skapa den virtuella datorn i Azure DevTest Lab-instans med hjälp av den aktivitet som har utvecklats speciellt för detta ändamål. 

1. I version-definition väljer **lägga till aktiviteter**.
2. På den **distribuera** lägger du till en *Skapa virtuell dator i Azure DevTest Labs* aktivitet. Konfigurera aktiviteten enligt följande:

   > [!NOTE]
   > För att skapa den virtuella datorn ska användas för efterföljande distributioner, se [Azure DevTest Labs uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. För **Azure-prenumeration RM**, Välj en anslutning i den **tillgängliga Azure Service anslutningar** listan eller skapa en mer begränsad behörighet för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager tjänstslutpunkten](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. För **Labbnamnet**, Välj namnet på den instans som du skapade tidigare.

   c. För **mallnamn**, ange den fullständiga sökvägen och namnet på mallfil som du sparade i din kod källdatabasen. Du kan använda inbyggda egenskaperna för versionshantering för att förenkla sökväg, till exempel:

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. För **mallparametrar**, ange parametrarna för variabler som definieras i mallen. Använd namnen på de variabler som du har definierat i miljön, till exempel:

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. För **utdata variabler - labb VM-ID**, behöver du ID för den nya virtuella datorn för efterföljande steg. Du ställer in standardnamnet för miljövariabeln som fylls automatiskt med detta ID i den **Utdatavariabler** avsnitt. Du kan redigera variabel om det behövs, men kom ihåg att använda rätt namn i efterföljande aktiviteter. Lab VM-ID är skriven i följande format:

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

3. Kör skriptet som du skapade tidigare för att samla in information om DevTest Labs VM. 
4. I version-definition väljer **lägga till aktiviteter** och klicka sedan på den **distribuera** lägger du till en *Azure PowerShell* aktivitet. Konfigurera aktiviteten enligt följande:

   > [!NOTE]
   > Om du vill samla in information om DevTest Labs VM finns [distribuera: Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) och kör skriptet.

   a. För **Azure anslutningstypen**väljer **Azure Resource Manager**.

   b. För **Azure-prenumeration RM**, Välj en anslutning i listan under **tillgängliga Azure Service anslutningar**, eller skapa en mer begränsad behörighet för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager tjänstslutpunkten](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   c. För **skripttypen**väljer **skriptfilen**.
 
   d. För **skriptsökvägen**, ange den fullständiga sökvägen och namnet på det skript som du sparade i din kod källdatabasen. Du kan använda inbyggda egenskaperna för versionshantering för att förenkla sökväg, till exempel:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. För **skriptargument**, ange namnet på den miljövariabel som var fylls automatiskt med labbet VM-ID för den föregående aktiviteten till exempel: 
      ```
      -labVmId '$(labVMId)'
      ```
    Skriptet samlar in de efterfrågade värdena och lagrar dem i miljövariabler i definitionen versionen så att du lätt kan se dem i efterföljande steg.

5. Distribuera din app till den nya DevTest Labs VM. Uppgifter som du vanligtvis använder för att distribuera appen som är *Azure filkopiering* och *PowerShell på måldatorerna*.
   Information om den virtuella datorn som du behöver för parametrarna för dessa uppgifter är lagrad i tre configuration variabler med namnet **labVmRgName**, **labVMIpAddress**, och **labVMFqdn**i definitionen för versionen. Du kan hoppa över det här steget om du bara vill experimentera med att skapa ett DevTest Labs VM och en anpassad avbildning utan att distribuera en app till den.

### <a name="create-an-image"></a>Skapa en avbildning

Nästa steg är att skapa en avbildning av den nyligen distribuerade virtuella datorn i Azure DevTest Labs-instans. Du kan sedan använda avbildningen för att skapa kopior av den virtuella datorn på begäran när du vill köra en aktivitet som dev eller köra vissa tester. 

1. I version-definition väljer **lägga till aktiviteter**.
2. På den **distribuera** lägger du till en **Azure DevTest Labs Skapa anpassad bild** aktivitet. Konfigurera enligt följande:

   > [!NOTE]
   > För att skapa avbildningen Se [Azure DevTest Labs uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. För **Azure-prenumeration RM**i den **tillgängliga Azure Service anslutningar** listan, Välj en anslutning från listan eller skapa en mer begränsad behörighet för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager tjänstslutpunkten](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. För **Labbnamnet**, Välj namnet på den instans som du skapade tidigare.

   c. För **anpassade avbildningsnamn**, ange ett namn för den anpassade avbildningen.

   d. (Valfritt) För **beskrivning**, ange en beskrivning som gör det lättare att välja rätt avbildning senare.

   e. För **källa Lab VM - ID för meddelandekälla Lab VM**, om du har ändrat standardnamnet för miljövariabeln som var fylls automatiskt med ID labbet VM av en tidigare aktivitet redigera den här. Standardvärdet är **$(labVMId)**.

   f. För **utdata variabler - ID för anpassad bild**, behöver du ID för den nyligen skapade avbildningen när du vill hantera eller ta bort den. Standardnamnet för miljövariabeln som fylls automatiskt med detta ID anges i den **Utdatavariabler** avsnitt. Du kan redigera variabeln om det behövs.

### <a name="delete-the-vm"></a>Ta bort den virtuella datorn

Det sista steget är att ta bort den virtuella datorn som du distribuerade i Azure DevTest Labs-instans. Du skulle normalt tar bort den virtuella datorn när du kör dev uppgifterna eller köra testerna som du behöver på den distribuerade virtuella datorn. 

1. I version-definition väljer **lägga till aktiviteter** och klicka sedan på den **distribuera** lägger du till en *ta bort virtuell dator i Azure DevTest Labs* aktivitet. Konfigurera enligt följande:

      > [!NOTE]
      > Om du vill ta bort den virtuella datorn finns [Azure DevTest Labs uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. För **Azure-prenumeration RM**, Välj en anslutning i den **tillgängliga Azure Service anslutningar** listan eller skapa en mer begränsad behörighet för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager tjänstslutpunkten](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
 
   b. För **Lab VM-ID**, om du har ändrat standardnamnet för miljövariabeln som var fylls automatiskt med ID labbet VM av en tidigare aktivitet redigera den här. Standardvärdet är **$(labVMId)**.

2. Ange ett namn för versionen definitionen och spara den.
3. Skapa en ny version, väljer du den senaste versionen och distribuera den till den enda miljön i definitionen.

Uppdatera vyn i din instans DevTest Labs i Azure portal för att visa den virtuella datorn och avbildning som skapas och den virtuella datorn som tas bort igen i varje steg.

Du kan nu använda den anpassade avbildningen för att skapa virtuella datorer när de är obligatoriska.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapa flera Virtuella miljöer med Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
* Utforska mer Resource Manager-snabbstartsmallar för DevTest Labs automation från den [offentliga DevTest Labs GitHub-repo-](https://github.com/Azure/azure-quickstart-templates).
* Om det behövs, finns det [VSTS felsökning](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting) sidan.
 
