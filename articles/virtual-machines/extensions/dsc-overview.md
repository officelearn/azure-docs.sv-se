---
title: Desired State Configuration för översikt över Azure
description: Lär dig hur du använder Microsoft Azure-tilläggshanterare för PowerShell Desired State Configuration (DSC). Artikeln innehåller förutsättningar, arkitektur och cmdletar.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: DSC
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: 18d6478763fd6551cc8baac6ea54e8d91f1a28e6
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45629976"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introduktion till Azure Desired State Configuration-tilläggshanterare

Azure VM-agenten och dess associerade tillägg är en del av Microsoft Azure-infrastrukturtjänster. VM-tillägg är programkomponenter som utökar funktionerna för virtuell dator och förenkla olika VM-hanteringsåtgärder.

Primärt användningsfall för tillägget Azure Desired State Configuration (DSC) är ska kunna starta en virtuell dator till den [Azure Automation DSC-tjänsten](../../automation/automation-dsc-overview.md). Start av en virtuell dator innehåller [fördelar](/powershell/dsc/metaconfig#pull-service) som omfattar kontinuerlig hantering av VM-konfiguration och integrering med andra operativa verktyg, till exempel Azure-övervakning.

Du kan använda DSC-tillägget oberoende av Automation DSC-tjänsten. Detta innebär dock att en enda åtgärd som uppstår under distributionen. Inga pågående rapportering eller konfigurationshantering är tillgänglig, än lokalt på den virtuella datorn.

Den här artikeln innehåller information om båda scenarierna: med hjälp av DSC-tillägget för Automation-onboarding och med hjälp av DSC-tillägget som ett verktyg för att tilldela konfigurationer för virtuella datorer med hjälp av Azure SDK.

## <a name="prerequisites"></a>Förutsättningar

- **Lokal dator**: för att interagera med Azure VM-tillägget, måste du använda Azure portal eller Azure PowerShell SDK.
- **Gästagenten**: den virtuella Azure-datorer som har konfigurerats av DSC-konfigurationen måste vara ett operativsystem som stöder Windows Management Framework (WMF) 4.0 eller senare. En fullständig lista över operativsystemversioner som stöds finns i den [versionshistorik för DSC-tillägget](/powershell/dsc/azuredscexthistory).

## <a name="terms-and-concepts"></a>Termer och begrepp

Den här guiden förutsätter förtrogenhet med följande begrepp:

- **Konfigurationen**: A DSC konfigurationsdokumentet.
- **Noden**: ett mål för en DSC-konfiguration. I det här dokumentet *noden* alltid refererar till en Azure-dator.
- **Konfigurationsdata**: en .psd1-fil som har miljödata för en konfiguration.

## <a name="architecture"></a>Arkitektur

Azure DSC-tillägget använder ramen för Azure VM-agenten för att leverera, tillämpar och rapportera om DSC-konfigurationer som körs på virtuella Azure-datorer. DSC-tillägget accepterar en konfigurationsdokumentet och en uppsättning parametrar. Om ingen fil har angetts och en [standard konfigurationsskript](#default-configuration-script) bäddas med tillägget. Standard-konfigurationsskript används endast för att ange metadata [lokal konfigurationshanterare](/powershell/dsc/metaconfig).

När tillägget anropas för första gången installerar en version av WMF med hjälp av följande logik:

- Om Azure VM-Operativsystemet är Windows Server 2016, utförs ingen åtgärd. Windows Server 2016 har redan den senaste versionen av PowerShell installerad.
- Om den **wmfVersion** egenskapen har angetts, den versionen av WMF är installerad, såvida inte den här versionen är inte kompatibel med den Virtuella datorns operativsystem.
- Om ingen **wmfVersion** egenskapen har angetts, den senaste tillämpliga versionen av WMF är installerad.

Installera WMF kräver en omstart. Efter omstart tillägget laddar ned .zip-filen som anges i den **modulesUrl** egenskapen om anges. Om den här platsen är i Azure Blob storage, kan du ange en SAS-token i den **sasToken** egenskapen för åtkomst till filen. När .zip hämtas och packa upp, configuration-funktionen anges i **configurationFunction** körs ska generera en MOF-fil. Tillägget kör sedan `Start-DscConfiguration -Force` med hjälp av genererade MOF-filen. Tillägget samlar in utdata och skriver den till Azure-status-kanalen.

### <a name="default-configuration-script"></a>Standard konfigurationsskript

Azure DSC-tillägget innehåller ett standard-konfigurationsskript som har avsedd att vara används när du publicerar en virtuell dator till Azure Automation DSC-tjänsten. Skriptparametrarna ligger i linje med konfigurerbara egenskaper för [lokal konfigurationshanterare](/powershell/dsc/metaconfig). Skriptparametrar Se [standard konfigurationsskript](dsc-template.md#default-configuration-script) i [Desired State Configuration-tillägget med Azure Resource Manager-mallar](dsc-template.md). Fullständigt skript finns i den [Azure snabbstarts-mall i GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-resource-manager-templates"></a>DSC-tillägget i Resource Manager-mallar

I de flesta fall är Resource Manager-distributionsmallar det förväntade sättet att arbeta med DSC-tillägget. Mer information och exempel på hur du lägger till DSC-tillägg i mallar för Resource Manager-distribution finns i [Desired State Configuration-tillägget med Azure Resource Manager-mallar](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>DSC-tillägget PowerShell-cmdletar

PowerShell-cmdlets som används för att hantera DSC-tillägget bäst i interaktiva felsökning och scenarier för insamling av information. Du kan använda cmdlets för att paketera, publicera och övervaka distribution av DSC-tillägget. Cmdlet: ar för DSC-tillägget inte ännu har uppdaterats för att fungera med den [standard konfigurationsskript](#default-configuration-script).

Den **publicera AzureRmVMDscConfiguration** cmdlet tar i en konfigurationsfil, söker beroende DSC-resurser och skapar sedan en .zip-fil. ZIP-filen innehåller konfigurations- och DSC-resurser som krävs för att införa konfigurationen. Cmdlet: en kan också skapa paketet lokalt genom att använda den *- OutputArchivePath* parametern. I annat fall cmdlet: en publiceras .zip-filen till blob storage och skyddar den med en SAS-token.

.Ps1-konfigurationsskript som cmdleten skapar är i ZIP-filen i roten på arkivmappen. Modulmappen placeras i arkivmapp i resurser.

Den **Set-AzureRmVMDscExtension** cmdlet lägger in de inställningar som kräver att PowerShell DSC-tillägg i en VM-konfigurationsobjektet.

Den **Get-AzureRmVMDscExtension** cmdlet hämtar status för DSC-tillägg för en specifik virtuell dator.

Den **Get-AzureRmVMDscExtensionStatus** cmdlet: en hämtar status för DSC-konfiguration som trätt i kraft av hanteraren för DSC-tillägget. Den här åtgärden kan utföras på en enskild virtuell dator eller på en grupp virtuella datorer.

Den **Remove-AzureRmVMDscExtension** cmdlet tar bort tillägget hanteraren från en specifik virtuell dator. Denna cmdlet har *inte* ta bort konfigurationen, avinstallera WMF eller ändra inställningarna som används på den virtuella datorn. Det bara tar bort tillägg för hanteraren. 

Viktig information om cmdlet: ar för resurshanteraren DSC-tillägg:

- Azure Resource Manager-cmdletar är synkrona.
- Den *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version*, och *plats*parametrar krävs.
- *ArchiveResourceGroupName* är en valfri parameter. Du kan ange den här parametern när ditt storage-konto som hör till en annan resursgrupp än den där den virtuella datorn skapas.
- Använd den **AutoUpdate** växel för att automatiskt uppdatera tillägget hanteraren till den senaste versionen när den är tillgänglig. Den här parametern har kan orsaka omstarter på den virtuella datorn när en ny version av WMF har släppts.

### <a name="get-started-with-cmdlets"></a>Kom igång med cmdlet: ar

Azure DSC-tillägget kan använda dokument för DSC-konfiguration för att konfigurera Azure virtuella datorer direkt under distributionen. Det här steget registrera inte noden till Automation. Noden är *inte* hanteras centralt.

I följande exempel visas ett enkelt exempel på en konfiguration. Spara konfigurationen lokalt som IisInstall.ps1.

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

Följande kommandon kan du placera skriptet IisInstall.ps1 på den angivna virtuella datorn. Kommandona också kör konfigurationen och rapportera tillbaka statusen.

```powershell
$resourceGroup = 'dscVmDemo'
$location = 'westus'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate $true -ConfigurationName 'IISInstall'
```

## <a name="azure-portal-functionality"></a>Azure portal-funktioner

Konfigurera DSC i portalen:

1. Gå till en virtuell dator.
2. Under **Inställningar** väljer du **Tillägg**.
3. I den nya sidan som skapas, väljer **+ Lägg till**, och välj sedan **PowerShell Desired State Configuration**.
4. Klicka på **skapa** längst ned på tilläggssidan.

Portalen samlar in följande indata:

- **Konfiguration av moduler eller skript**: det här fältet är obligatoriskt (formuläret har inte uppdaterats för den [standard konfigurationsskript](#default-configuration-script)). Konfiguration av moduler och skript kräver en .ps1-fil som har ett konfigurationsskript eller en .zip-fil med en .ps1-konfigurationsskript i roten. Om du använder en .zip-fil, måste alla beroende resurser inkluderas i modulen mappar i .zip. Du kan skapa ZIP-filen med hjälp av den **publicera AzureVMDscConfiguration - OutputArchivePath** cmdlet som ingår i Azure PowerShell SDK. ZIP-filen laddas upp till blobblagringen användare och skyddas av en SAS-token.

- **Modulkvalificerade namn Configuration**: du kan inkludera flera configuration-funktioner i en .ps1-fil. Ange namnet på konfigurationsskript för .ps1 följt av \\ och namnet på funktionen konfiguration. Till exempel om .ps1 skriptet har namnet configuration.ps1 och konfigurationen är **IisInstall**, ange **configuration.ps1\IisInstall**.

- **Konfiguration av argument**: om configuration-funktionen tar argument kan du ange dem här i formatet **argumentName1 = värde1, argumentName2 = värde2**. Det här formatet är ett annat format där configuration argument accepteras i PowerShell-cmdlets eller Resource Manager-mallar.

- **PSD1 för Konfigurationsdatafilen**: det här fältet är valfritt. Om din konfiguration kräver en konfigurationsfil för data i .psd1, kan du använda det här fältet för att välja datafältet och överföra den till blobblagringen användare. Konfigurationsdatafilen skyddas av en SAS-token i blob storage.

- **WMF Version**: Anger vilken version av Windows Management Framework (WMF) och som ska installeras på den virtuella datorn. Denna egenskap anges till senaste installerar den senaste versionen av WMF. För närvarande endast möjliga värden för den här egenskapen är 4.0, 5.0, 5.1, och de senaste. Dessa möjliga värden är föremål för uppdateringar. Standardvärdet är **senaste**.

- **Datainsamling**: Anger om tillägget samlar in telemetri. Mer information finns i [Azure DSC-tillägg-datainsamling](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/).

- **Version**: Anger versionen för DSC-tillägget för att installera. Information om versioner finns i [versionshistorik för DSC-tillägget](/powershell/dsc/azuredscexthistory).

- **Automatiskt uppgradera delversion**: det här fältet som mappar till den **AutoUpdate** växla i cmdlets och aktiverar tillägget att uppdatera till den senaste versionen automatiskt under installationen. **Ja** instruerar tilläggshanterare att använda den senaste tillgängliga versionen och **nr** tvingar den **Version** angivna installeras. Att välja varken **Ja** eller **nr** är detsamma som att välja **nr**.

## <a name="logs"></a>Logs

Loggar för tillägget lagras på följande plats: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Nästa steg

- Mer information om PowerShell DSC, går du till den [PowerShell dokumentationscentret](/powershell/dsc/overview).
- Granska den [Resource Manager-mall för DSC-tillägget](dsc-template.md).
- Fler funktioner som du kan hantera med hjälp av PowerShell DSC, samt mer DSC-resurser, bläddra i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Mer information om att skicka känslig parametrar i konfigurationer finns i [hantera autentiseringsuppgifter på ett säkert sätt med DSC-tilläggshanterare](dsc-credentials.md).