---
title: Översikt över önskad tillståndskonfiguration för Azure
description: Lär dig hur du använder Microsoft Azure-tilläggshanteraren för PowerShell Desired State Configuration (DSC). Artikeln innehåller förutsättningar, arkitektur och cmdlets.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: Dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: dcb63031e6c033ce2372dc05e588b0f54cb1609f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294803"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introduktion till tilläggshanteraren för Azure Desired State Configuration

Azure VM-agenten och tillhörande tillägg är en del av Microsoft Azure-infrastrukturtjänster. VM-tillägg är programvarukomponenter som utökar VM-funktioner och förenklar olika vm-hanteringsåtgärder.

Det primära användningsfallet för DSC-tillägget (Azure Desired State Configuration) är att bootstrap a VM to the [Azure Automation State Configuration (DSC) service](../../automation/automation-dsc-overview.md).
Tjänsten ger [fördelar](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) som inkluderar löpande hantering av VM-konfigurationen och integration med andra operativa verktyg, till exempel Azure Monitoring.
Att använda tillägget för att registrera virtuella datorer till tjänsten ger en flexibel lösning som även fungerar över Azure-prenumerationer.

Du kan använda DSC-tillägget oberoende av Automation DSC-tjänsten.
Detta kommer dock bara att driva en konfiguration till den virtuella datorn.
Ingen löpande rapportering är tillgänglig, förutom lokalt i den virtuella datorn.

Den här artikeln innehåller information om båda scenarierna: användning av DSC-tillägget för onboarding av automatisering och användning av DSC-tillägget som ett verktyg för att tilldela konfigurationer till virtuella datorer med hjälp av Azure SDK.

## <a name="prerequisites"></a>Krav

- **Lokal dator**: Om du vill interagera med Azure VM-tillägget måste du använda antingen Azure-portalen eller Azure PowerShell SDK.
- **Gästagent:** Den virtuella Azure-datorn som konfigureras av DSC-konfigurationen måste vara ett operativsystem som stöder Windows Management Framework (WMF) 4.0 eller senare. Den fullständiga listan över os-versioner som stöds finns i [versionshistoriken för DSC-tillägg](/powershell/scripting/dsc/getting-started/azuredscexthistory).

## <a name="terms-and-concepts"></a>Termer och begrepp

Den här guiden förutsätter förtrogenhet med följande begrepp:

- **Konfiguration**: Ett DSC-konfigurationsdokument.
- **Nod**: Ett mål för en DSC-konfiguration. I det här dokumentet refererar noden alltid till en Virtuell Azure.In this document, *node* always refers to an Azure VM.
- **Konfigurationsdata**: En PSD1-fil som har miljödata för en konfiguration.

## <a name="architecture"></a>Arkitektur

Azure DSC-tillägget använder Azure VM Agent-ramverket för att leverera, anta och rapportera om DSC-konfigurationer som körs på virtuella Azure-datorer. DSC-tillägget accepterar ett konfigurationsdokument och en uppsättning parametrar. Om ingen fil anges bäddas ett [standardkonfigurationsskript](#default-configuration-script) in med tillägget. Standardkonfigurationsskriptet används endast för att ange metadata i [Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

När tillägget anropas för första gången installeras en version av WMF med hjälp av följande logik:

- Om Azure VM OS är Windows Server 2016 vidtas ingen åtgärd. Windows Server 2016 har redan den senaste versionen av PowerShell installerad.
- Om **wmfVersion-egenskapen** anges installeras den versionen av WMF, såvida inte den versionen är inkompatibel med den virtuella datorns operativsystem.
- Om ingen **wmfVersion-egenskap** har angetts installeras den senaste aktuella versionen av WMF.

Installera WMF kräver en omstart. När du har startat om hämtas tillägget från ZIP-filen som anges i egenskapen **modulesUrl,** om den anges. Om den här platsen finns i Azure Blob-lagring kan du ange en SAS-token i egenskapen **sasToken** för åtkomst till filen. När ZIP har hämtats och packats upp körs konfigurationsfunktionen som definieras i **configurationFunction** för att generera en .mof([Managed Object Format)](https://docs.microsoft.com/windows/win32/wmisdk/managed-object-format--mof-)fil. Tillägget körs `Start-DscConfiguration -Force` sedan med hjälp av den genererade .mof-filen. Tillägget samlar in utdata och skriver det till Azure-statuskanalen.

### <a name="default-configuration-script"></a>Standardkonfigurationsskript

Azure DSC-tillägget innehåller ett standardkonfigurationsskript som är avsett att användas när du går ombord på en virtuell dator till Azure Automation DSC-tjänsten. Skriptparametrarna är justerade med de konfigurerbara egenskaperna [för Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig). Skriptparametrar finns i [Standardkonfigurationsskript](dsc-template.md#default-configuration-script) i [tillägget Konfiguration för önskat tillstånd med Azure Resource Manager-mallar](dsc-template.md). Det fullständiga skriptet finns [i snabbstartsmallen för Azure i GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Information för registrering med DSC-tjänsten (Azure Automation State Configuration)

När du använder DSC-tillägget för att registrera en nod med tjänsten Tillståndskonfiguration måste tre värden anges.

- RegistrationUrl - https-adressen för Azure Automation-kontot
- RegistrationKey - en delad hemlighet som används för att registrera noder med tjänsten
- NodeConfigurationName - namnet på nodkonfigurationen (MOF) som ska hämtas från tjänsten för att konfigurera serverrollen

Den här informationen kan ses i [Azure-portalen](../../automation/automation-dsc-onboarding.md#onboard-a-vm-using-azure-portal) eller så kan du använda PowerShell.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

Kontrollera att nodkonfigurationsnamnet finns i Azure State Configuration för nodkonfigurationen.  Om den inte gör det returneras ett fel i tilläggsdistributionen.  Kontrollera också att du använder namnet på *nodkonfigurationen* och inte konfigurationen.
En konfiguration definieras i ett skript som används [för att kompilera MOF-filen (Nod Configuration).](https://docs.microsoft.com/azure/automation/automation-dsc-compile)
Namnet kommer alltid att vara konfigurationen följt av en punkt `.` och antingen `localhost` eller ett visst datornamn.

## <a name="dsc-extension-in-resource-manager-templates"></a>DSC-tillägg i Resource Manager-mallar

I de flesta fall är Resource Manager-distributionsmallar det förväntade sättet att arbeta med DSC-tillägget. Mer information och exempel på hur du inkluderar DSC-tillägget i Resurshanterarens distributionsmallar finns i [Tillägget För önskad tillståndskonfiguration med Azure Resource Manager-mallar](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>PowerShell-cmdlets för DSC-förlängning

De PowerShell-cmdletar som används för att hantera DSC-tillägget används bäst i interaktiva felsöknings- och informationsinsamlingsscenarier. Du kan använda cmdlets för att paketera, publicera och övervaka DSC-tilläggsdistributioner. Cmdlets för DSC-tillägget är ännu inte uppdaterade för att fungera med [standardkonfigurationsskriptet](#default-configuration-script).

**Cmdlet för Publicering-AzVMDscKonfigurering** tar i en konfigurationsfil, söker efter beroende DSC-resurser och skapar sedan en ZIP-fil. Zip-filen innehåller konfigurations- och DSC-resurser som behövs för att anta konfigurationen. Cmdleten kan också skapa paketet lokalt med parametern *-OutputArchivePath.* Annars publicerar cmdleten ZIP-filen till blob-lagring och skyddar den sedan med en SAS-token.

Konfigurationsskriptet .ps1 som cmdlet skapar finns i ZIP-filen i arkivmappens rot. Modulmappen placeras i arkivmappen i resurser.

**Cmdlet set-AzVMDscExtension** ingjuter de inställningar som PowerShell DSC-tillägget kräver i ett vm-konfigurationsobjekt.

**Cmdleten Get-AzVMDscExtension** hämtar DSC-tilläggsstatus för en viss virtuell dator.

**Cmdlet Get-AzVMDscExtensionStatus** hämtar statusen för DSC-konfigurationen som har antagits av DSC-tilläggshanteraren. Den här åtgärden kan utföras på en enda virtuell dator eller på en grupp virtuella datorer.

Cmdleten **Remove-AzVMDscExtension** tar bort förlängningshanteraren från en viss virtuell dator. Den här cmdleten tar *inte* bort konfigurationen, avinstallerar WMF eller ändrar de tillämpade inställningarna på den virtuella datorn. Förlängningshanteraren tas bara bort. 

Viktig information om DSC-tilläggs-cmdlets för Resurshanteraren:

- Azure Resource Manager-cmdlets är synkrona.
- *Parametrarna ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version*och *Plats* krävs alla.
- *ArchiveResourceGroupName* är en valfri parameter. Du kan ange den här parametern när ditt lagringskonto tillhör en annan resursgrupp än den där den virtuella datorn skapas.
- Använd växeln **AutoUpdate** för att automatiskt uppdatera tilläggshanteraren till den senaste versionen när den är tillgänglig. Den här parametern kan orsaka omstarter på den virtuella datorn när en ny version av WMF släpps.

### <a name="get-started-with-cmdlets"></a>Komma igång med cmdlets

Azure DSC-tillägget kan använda DSC-konfigurationsdokument för att direkt konfigurera Virtuella Azure-datorer under distributionen. Det här steget registrerar inte noden till Automation. Noden hanteras *inte* centralt.

Följande exempel visar ett enkelt exempel på en konfiguration. Spara konfigurationen lokalt som iisInstall.ps1.

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

Följande kommandon placerar skriptet iisInstall.ps1 på den angivna virtuella datorn. Kommandona kör också konfigurationen och rapporterar sedan tillbaka statusen.

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera DSC-tillägget till en befintlig virtuell dator.

För en virtuell dator som kör Windows:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

För en virtuell dator som kör Linux:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Azure-portalfunktioner

Så här konfigurerar du DSC i portalen:

1. Gå till en virtuell dator.
2. Under **Inställningar** väljer du **Tillägg**.
3. På den nya sidan som skapas väljer du **+ Lägg till**och väljer sedan **PowerShell-önskad tillståndskonfiguration**.
4. Klicka på **Skapa** längst ned på tilläggsinformationssidan.

Portalen samlar in följande indata:

- **Konfigurationsmoduler eller skript:** Det här fältet är obligatoriskt (formuläret har inte uppdaterats för [standardkonfigurationsskriptet](#default-configuration-script)). Konfigurationsmoduler och skript kräver en PS1-fil som har ett konfigurationsskript eller en ZIP-fil med ett PS1-konfigurationsskript vid roten. Om du använder en ZIP-fil måste alla beroende resurser inkluderas i modulmapparna i ZIP.If you use a .zip file, all dependent resources must be included in module folders in the .zip. Du kan skapa ZIP-filen med hjälp av cmdleten **Publish-AzureVMDscConfiguration -OutputArchivePath** som ingår i Azure PowerShell SDK. Zip-filen överförs till användarens blob-lagring och skyddas av en SAS-token.

- **Modulkvalificerat konfigurationsnamn:** Du kan inkludera flera konfigurationsfunktioner i en PS1-fil. Ange namnet på konfigurationen .ps1-skript följt av \\ och namnet på konfigurationsfunktionen. Om PS1-skriptet till exempel har namnet configuration.ps1 och konfigurationen är **IisInstall**anger du **configuration.ps1\IisInstall**.

- **Konfigurationsargument**: Om konfigurationsfunktionen tar argument anger du dem här i formatet **argumentName1=value1,argumentName2=value2**. Det här formatet är ett annat format där konfigurationsargument accepteras i PowerShell-cmdletar eller Resource Manager-mallar.

- **PSD1-fil för konfigurationsdata:** Det här fältet är valfritt. Om konfigurationen kräver en konfigurationsdatafil i PSD1 använder du det här fältet för att välja datafältet och överför det till användarens blob-lagring. Konfigurationsdatafilen skyddas av en SAS-token i blob-lagring.

- **WMF-version:** Anger den version av Windows Management Framework (WMF) som ska installeras på den virtuella datorn. Om du ställer in den här egenskapen på den senaste versionen av WMF installeras den senaste versionen av WMF. För närvarande är de enda möjliga värdena för den här egenskapen 4.0, 5.0, 5.1 och senaste. Dessa möjliga värden är föremål för uppdateringar. Standardvärdet är **det senaste**.

- **Datainsamling:** Avgör om tillägget ska samla in telemetri. Mer information finns i [Azure DSC-datainsamling](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/)för tillägg .

- **Version**: Anger vilken version av DSC-tillägget som ska installeras. Information om versioner finns i [DSC-tilläggsversionshistorik](/powershell/scripting/dsc/getting-started/azuredscexthistory).

- **Automatisk uppgradering Minor Version:** Det här fältet mappar till **Växeln AutoUpdate** i cmdlets och gör det möjligt för tillägget att automatiskt uppdatera till den senaste versionen under installationen. **Ja** instruerar tilläggshanteraren att använda den senast tillgängliga versionen och **Nej** tvingar den **version som** har angetts att installeras. Om du varken väljer **Ja** eller **Nej** är samma sak som att välja **Nej**.

## <a name="logs"></a>Loggar

Loggar för tillägget lagras på följande plats:`C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Nästa steg

- Mer information om PowerShell DSC finns i [PowerShells dokumentationscenter](/powershell/scripting/dsc/overview/overview).
- Undersök [resurshanterarens mall för DSC-tillägget](dsc-template.md).
- Om du vill ha fler funktioner som du kan hantera med PowerShell DSC och mer DSC-resurser bläddrar du i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Mer information om hur du skickar känsliga parametrar till konfigurationer finns i [Hantera autentiseringsuppgifter på ett säkert sätt med DSC-tilläggshanteraren](dsc-credentials.md).
