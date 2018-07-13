---
title: Integrera Azure DevTest Labs i din VSTS kontinuerlig integrering och leverans-pipeline | Microsoft Docs
description: Lär dig hur du integrerar Azure DevTest Labs i din VSTS kontinuerlig integrering och leverans pipeline
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38635514"
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>Integrera Azure DevTest Labs i din VSTS kontinuerlig integrering och leverans pipeline
Du kan använda den *Azure DevTest Labs-uppgifter* som är installerad i Visual Studio Team Services (VSTS) att enkelt integrera din version och versionen CI/CD-pipeline med Azure DevTest Labs. Tillägget installeras tre uppgifter: 
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

   När du kör det här skriptet på agenten som en del av versionsdefinitionen och om du använder uppgift som *Azure File Copy* eller *PowerShell på måldatorer*, skriptet samlar in de värden som du behöver distribuera din app till den virtuella datorn. Du skulle normalt använda dessa uppgifter för att distribuera appar till en Azure-dator. Uppgifterna kan du kräva värden som namn på resursgrupp för virtuell dator, IP-adress och fullständigt kvalificerade domännamnet (fullständigt domännamn).

## <a name="create-a-release-definition-in-release-management"></a>Skapa en versionsdefinition i Release Management
Om du vill skapa versionsdefinitionen, gör du följande:

1. På den **versioner** fliken den **Build & Release** hubb, klicka på plustecknet (+).
2. I den **skapa versionsdefinition** väljer den **tom** mallen och välj sedan **nästa**.
3. Välj **väljer senare**, och välj sedan **skapa** att skapa en ny versionsdefinition med en standardmiljö och inga länkade artefakter.
4. Om du vill öppna snabbmenyn i den nya versionsdefinition, Välj ellipsen (...) bredvid miljönamnet på, och därefter **konfigurera variabler**. 
5. I den **konfigurera - miljö** fönster för de variabler som du använder i definitionen publiceringsuppgifter anger du följande värden:

   a. För **vmName**, anger du namnet som tilldelats den virtuella datorn när du skapade Resource Manager-mall i Azure-portalen.

   b. För **användarnamn**, anger du användarnamnet som du tilldelade till den virtuella datorn när du skapade Resource Manager-mall i Azure-portalen.

   c. För **lösenord**, ange lösenordet som du tilldelade till den virtuella datorn när du skapade Resource Manager-mall i Azure-portalen. Använd hänglåsikonen ”” för att dölja och skydda lösenordet.

### <a name="create-a-vm"></a>Skapa en virtuell dator

Nästa steg i distributionen är att skapa den virtuella datorn ska användas som ”gyllene bild” för efterföljande distributioner. Du kan skapa den virtuella datorn i Azure DevTest Lab-instans med hjälp av den aktivitet som har utvecklats särskilt för detta ändamål. 

1. Välj i versionsdefinitionen, **lägga till aktiviteter**.
2. På den **distribuera** fliken, lägga till en *Skapa virtuell dator i Azure DevTest Labs* uppgift. Konfigurera aktiviteten enligt följande:

   > [!NOTE]
   > Om du vill skapa den virtuella datorn ska användas för efterföljande distributioner [Azure DevTest Labs-uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. För **Azure RM-prenumeration**, Välj ett projekt i den **tillgängliga Azure-Tjänstanslutningar** , eller skapa en mer begränsade behörigheter för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager-tjänstslutpunkt](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

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

3. Kör skriptet som du skapade tidigare för att samla in information om DevTest Labs-VM. 
4. I versionsdefinitionen av väljer **lägga till aktiviteter** och klicka sedan på den **distribuera** fliken, lägga till en *Azure PowerShell* uppgift. Konfigurera aktiviteten enligt följande:

   > [!NOTE]
   > Om du vill samla in information om DevTest Labs VM, se [distribuera: Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) och kör skriptet.

   a. För **Azure anslutningstypen**väljer **Azure Resource Manager**.

   b. För **Azure RM-prenumeration**, Välj en anslutning i listan under **tillgängliga Azure-Tjänstanslutningar**, eller skapa en mer begränsade behörigheter för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager-tjänstslutpunkt](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   c. För **Skripttyp**väljer **skriptfilen**.
 
   d. För **skriptets sökväg**, ange den fullständiga sökvägen och namnet på det skript som du sparade till lagringsplatsen för källkoden. Du kan använda inbyggda egenskaperna för versionshantering för att förenkla sökväg, till exempel:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. För **skriptargument**, anger du namnet på miljövariabeln som har fylls automatiskt med lab VM-ID för den föregående aktiviteten, till exempel: 
      ```
      -labVmId '$(labVMId)'
      ```
    Skriptet samlar in värdena som krävs och lagrar dem i miljövariabler i versionsdefinitionen så att du enkelt kan hänvisa till dem i efterföljande steg.

5. Distribuera din app till den nya DevTest Labs virtuella datorn. Uppgifter som du normalt använda för att distribuera appen är *Azure File Copy* och *PowerShell på måldatorer*.
   Information om den virtuella datorn som du behöver för parametrarna för uppgifterna lagras i tre configuration variabler med namnet **labVmRgName**, **labVMIpAddress**, och **labVMFqdn**inom versionsdefinitionen. Om du bara vill experimentera med att skapa en DevTest Labs virtuell dator och en anpassad avbildning, utan att distribuera en app kan du hoppa över det här steget.

### <a name="create-an-image"></a>Skapa en avbildning

Nästa steg är att skapa en avbildning av den nyligen distribuerade virtuella datorn i din Azure DevTest Labs-instans. Du kan sedan använda avbildningen för att skapa kopior av den virtuella datorn på begäran när du vill köra en dev-uppgift eller köra vissa tester. 

1. Välj i versionsdefinitionen, **lägga till aktiviteter**.
2. På den **distribuera** fliken, lägga till en **Azure DevTest Labs Skapa anpassad avbildning** uppgift. Konfigurera enligt följande:

   > [!NOTE]
   > För att skapa avbildningen, se [Azure DevTest Labs-uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. För **Azure RM-prenumeration**i den **tillgängliga Azure-Tjänstanslutningar** listan, Välj en anslutning i listan eller skapa en mer begränsade behörigheter för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager-tjänstslutpunkt](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. För **Labbnamn**, Välj namnet på den instans som du skapade tidigare.

   c. För **anpassade avbildningsnamn**, ange ett namn för den anpassade avbildningen.

   d. (Valfritt) För **beskrivning**, ange en beskrivning som gör det enkelt att välja rätt avbildning senare.

   e. För **Virtuella labb - VM-ID för meddelandekälla Lab**, om du har ändrat standardnamn för miljövariabeln som automatiskt har fyllts med ID för Virtuella labbdatorer med en tidigare uppgift, redigera den här. Standardvärdet är **$(labVMId)**.

   f. För **utdata variabler - ID för anpassad bild**, du behöver ID för den nyligen skapade avbildningen när du vill hantera eller ta bort den. Standardnamnet för miljövariabeln som fylls i automatiskt med detta ID anges i den **utdata variabler** avsnittet. Du kan redigera variabeln om det behövs.

### <a name="delete-the-vm"></a>Ta bort den virtuella datorn

Det sista steget är att ta bort den virtuella datorn som du distribuerade i din Azure DevTest Labs-instans. Du skulle normalt tar bort den virtuella datorn när du kör uppgifterna för utveckling eller köra testerna som du behöver på den distribuerade virtuella datorn. 

1. I versionsdefinitionen av väljer **lägga till aktiviteter** och klicka sedan på den **distribuera** fliken, lägga till en *ta bort virtuell dator i Azure DevTest Labs* uppgift. Konfigurera enligt följande:

      > [!NOTE]
      > Om du vill ta bort den virtuella datorn, se [Azure DevTest Labs-uppgifter](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. För **Azure RM-prenumeration**, Välj ett projekt i den **tillgängliga Azure-Tjänstanslutningar** , eller skapa en mer begränsade behörigheter för anslutning till din Azure-prenumeration. Mer information finns i [Azure Resource Manager-tjänstslutpunkt](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
 
   b. För **Lab VM-ID**, om du har ändrat standardnamn för miljövariabeln som automatiskt har fyllts med ID för Virtuella labbdatorer med en tidigare uppgift, redigera den här. Standardvärdet är **$(labVMId)**.

2. Ange ett namn för versionsdefinitionen och spara den.
3. Skapa en ny version, Välj den senaste versionen och distribuera den till den enda miljön i definitionen.

Uppdatera vyn för din DevTest Labs-instans i Azure portal för att visa den virtuella datorn och avbildning som skapas och den virtuella datorn som tas bort igen i varje steg.

Du kan nu använda den anpassade avbildningen för att skapa virtuella datorer när de är obligatoriska.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapa miljöer för flera virtuella datorer med Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
* Utforska fler Snabbstart Resource Manager-mallar för DevTest Labs automation från den [offentliga DevTest Labs GitHub-lagringsplatsen](https://github.com/Azure/azure-quickstart-templates).
* Om det behövs kan du se den [VSTS felsökning](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting) sidan.
 
