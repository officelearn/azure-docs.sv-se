---
title: Önskad Tillståndskonfiguration Azure översikt
description: 'Lär dig hur du använder Microsoft Azure-tillägget hanteraren för PowerShell önskad tillstånd Configuration DSC (). Artikeln innehåller krav, arkitektur och cmdlet: ar.'
services: virtual-machines-windows
documentationcenter: ''
author: DCtheGeek
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
ms.author: dacoulte
ms.openlocfilehash: 60560a4a656d0ad5df15208261ab8462f4271ec5
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introduktion till Azure Desired State Configuration-tillägg-hanterare

Azure VM-agenten och dess associerade tillägg är en del av Microsoft Azure infrastrukturtjänster. VM-tillägg är programkomponenter som utökar funktionerna för virtuell dator och förenkla olika VM-hanteringsåtgärder.

Primärt användningsfall för Azure önskad tillstånd Configuration DSC ()-tillägget är att starta en virtuell dator till den [Azure Automation DSC-tjänsten](../../automation/automation-dsc-overview.md). Startprogram för en virtuell dator innehåller [fördelar](/powershell/dsc/metaconfig#pull-service) som inkluderar kontinuerlig hantering av VM-konfiguration och integrering med andra operativa verktyg, till exempel Azure-övervakning.

Du kan använda DSC-tillägg oberoende av Automation DSC-tjänsten. Detta innebär dock en enda åtgärd som utförs under distributionen. Inga pågående reporting eller konfigurationshantering är tillgängligt, annat än lokalt på den virtuella datorn.

Den här artikeln innehåller information om båda scenarierna: DSC-tillägg för Automation onboarding och med hjälp av DSC-tillägg som ett verktyg för att tilldela konfigurationer av virtuella datorer med hjälp av Azure SDK.

## <a name="prerequisites"></a>Förutsättningar

- **Lokal dator**: ska interagera med Virtuella Azure-tillägget, måste du använda Azure-portalen eller Azure PowerShell SDK.
- **Gästagenten**: det virtuella Azure-datorn som har konfigurerats av DSC-konfigurationen måste vara ett operativsystem som stöder Windows Management Framework (WMF) 4.0 eller senare. En fullständig lista över operativsystemversioner som stöds finns i [DSC-tillägg versionshistorik](/powershell/dsc/azuredscexthistory).

## <a name="terms-and-concepts"></a>Termer och begrepp

Den här handboken förutsätter förtrogenhet med följande:

- **Konfigurationen**: A DSC-konfiguration dokumentet.
- **Noden**: ett mål för DSC-konfigurationen. I det här dokumentet *nod* alltid refererar till en Azure VM.
- **Konfigurationsdata**: en .psd1-fil som har miljödata för en konfiguration.

## <a name="architecture"></a>Arkitektur

Azure DSC-tillägg använder Azure VM-agenten framework för att leverera, tillämpar och rapportera om DSC-konfigurationer som körs på virtuella Azure-datorer. DSC-tillägg accepterar ett konfigurationsdokument och en uppsättning parametrar. Om ingen fil anges, en [standard konfigurationsskript](#default-configuration-script) bäddas med tillägget. Standard-konfigurationsskript används bara för att ange metadata i [Local Configuration Manager](/powershell/dsc/metaconfig).

När tillägget anropas för första gången, installeras en version av WMF med hjälp av följande logik:

- Om Azure VM OS är Windows Server 2016, utförs ingen åtgärd. Windows Server 2016 har redan den senaste versionen av PowerShell som är installerad.
- Om den **wmfVersion** egenskapen har angetts, den versionen av WMF har installerats, såvida inte den här versionen är inte kompatibel med den Virtuella datorns operativsystem.
- Om inget **wmfVersion** egenskapen har angetts, den senaste tillämpliga versionen av WMF har installerats.

Installera WMF kräver en omstart. Efter omstart, hämtar tillägget .zip-filen som anges i den **modulesUrl** egenskapen om. Om den här platsen är i Azure Blob storage, kan du ange en SAS-token i den **sasToken** egenskapen för att få åtkomst till filen. När .zip hämtas och packat upp, configuration-funktionen anges i **configurationFunction** körs ska generera en MOF-fil. Tillägget sedan körs `Start-DscConfiguration -Force` med hjälp av genererade MOF-filen. Tillägget avbildas utdata och skriver den till Azure status-kanalen.

### <a name="default-configuration-script"></a>Standard-konfigurationsskript

Azure DSC-tillägg innehåller ett standard-konfigurationsskript som har avsedd att vara används när du hanterar en virtuell dator till Azure Automation DSC-tjänsten. Skriptparametrarna ligger i linje med konfigurerbara egenskaper för [Local Configuration Manager](/powershell/dsc/metaconfig). Skriptparametrar finns [standard konfigurationsskript](dsc-template.md#default-configuration-script) i [Desired State Configuration-tillägget med Azure Resource Manager-mallar](dsc-template.md). Fullständig skriptet finns på [Azure quickstart mallen i GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-resource-manager-templates"></a>DSC-tillägg i Resource Manager-mallar

I de flesta fall är Resource Manager distributionsmallar det förväntade sättet att arbeta med DSC-tillägg. Mer information och exempel på hur du lägger till DSC-tillägg i mallar för distribution av hanteraren för filserverresurser finns [Desired State Configuration-tillägget med Azure Resource Manager-mallar](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>DSC-tillägg PowerShell-cmdlets

PowerShell-cmdletar som används för att hantera DSC-tillägg är bra att använda interaktiva felsöka och samla in information scenarier. Du kan använda cmdlets för att paketera, publicera och övervaka distributioner av DSC-tillägg. Cmdlets för DSC-tillägg ännu inte uppdaterats för att fungera med den [standard konfigurationsskript](#default-configuration-script).

Den **publicera AzureRmVMDscConfiguration** cmdlet tar i en konfigurationsfil, söker efter beroende DSC-resurser och skapar sedan en .zip-fil. ZIP-filen innehåller konfigurations- och DSC-resurser som behövs för att införa konfigurationen. Cmdlet kan också skapa paketet lokalt genom att använda den *- OutputArchivePath* parameter. Annars cmdlet publicerar ZIP-filen för att blob storage och skyddar den med en SAS-token.

.Ps1-konfigurationsskript som cmdleten skapar finns i ZIP-filen i roten på arkivmapp. Mappen modulen placeras i arkivmapp i resurser.

Den **Set AzureRmVMDscExtension** cmdlet lägger in de inställningar som kräver att PowerShell DSC-tillägg i ett VM-konfigurationsobjekt.

Den **Get-AzureRmVMDscExtension** cmdlet hämtar status för DSC-tillägg för en specifik VM.

Den **Get-AzureRmVMDscExtensionStatus** cmdlet hämtar status för DSC-konfigurationen trätt i kraft av DSC-tillägg-hanteraren. Den här åtgärden kan utföras på en enda virtuell dator eller på en grupp med virtuella datorer.

Den **ta bort AzureRmVMDscExtension** cmdlet tar bort tillägget hanteraren från en specifik VM. Denna cmdlet har *inte* ta bort konfigurationen, avinstallera WMF eller ändra inställningarna som används på den virtuella datorn. Endast hanteraren tillägget tas bort. 

Viktig information om cmdlet: ar för Resource Manager DSC-tillägg:

- Azure Resource Manager-cmdlets är synkrona.
- Den *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version*, och *plats*parametrar krävs.
- *ArchiveResourceGroupName* är en valfri parameter. Du kan ange den här parametern när ditt lagringskonto som hör till en annan resursgrupp än den där den virtuella datorn skapas.
- Använd den **automatisk uppdatering** växel för att automatiskt uppdatera hanteraren tillägget till den senaste versionen när den är tillgänglig. Den här parametern har kan orsaka omstarter på den virtuella datorn när en ny version av WMF släpps.

### <a name="get-started-with-cmdlets"></a>Kom igång med cmdlet: ar

Azure DSC-tillägg kan använda dokument för DSC-konfiguration för att konfigurera direkt Azure virtuella datorer under distributionen. Det här steget registrera inte noden till Automation. Noden är *inte* centralt hanterad.

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

Följande kommandon placera IisInstall.ps1 skriptet på den angivna virtuella datorn. Kommandona också köra konfigurationen och rapportera om status.

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

## <a name="azure-portal-functionality"></a>Azure portal funktioner

Att ställa in DSC i portalen:

1. Gå till en virtuell dator.
2. Under **inställningar**väljer **tillägg**.
3. I den nya sidan som har skapats, Välj **+ Lägg till**, och välj sedan **PowerShell Desired State Configuration**.
4. Klicka på **skapa** längst ned på sidan tillägg.

Portalen samlar in följande:

- **Konfiguration av moduler eller skript**: det här fältet är obligatoriskt (formuläret har inte uppdaterats för den [standard konfigurationsskript](#default-configuration-script)). Moduler för konfiguration och skript kräver en .ps1-fil som har ett konfigurationsskript eller en .zip-fil med en .ps1-konfigurationsskript i roten. Om du använder en .zip-fil, måste alla beroende resurser tas med i modulen mappar i .zip. Du kan skapa ZIP-filen med hjälp av den **publicera AzureVMDscConfiguration - OutputArchivePath** cmdlet som ingår i Azure PowerShell SDK. ZIP-filen har överförts till dina användare blob storage och skyddas av en SAS-token.

- **Modulkvalificerade namn Configuration**: du kan inkludera flera configuration-funktioner i en .ps1-fil. Ange namnet på .ps1 konfigurationsskript följt av \\ och namnet på funktionen konfiguration. Till exempel om .ps1 skriptet har namnet configuration.ps1 och konfigurationen är **IisInstall**, ange **configuration.ps1\IisInstall**.

- **Konfigurationen argument**: om funktionen configuration tar argument kan du ange dem här i formatet **argumentName1 = värde1, argumentName2 = värde2**. Det här är ett annat format där configuration argument accepteras i PowerShell-cmdlets eller Resource Manager-mallar.

- **Konfigurationsfilen Data PSD1**: det här fältet är valfritt. Om din konfiguration kräver en konfigurationsfil för data i .psd1, kan du använda det här fältet för att välja datafältet och överför den till dina användare blob storage. Konfigurationsfilen för data skyddas av en SAS-token i blob storage.

- **WMF Version**: Anger vilken version av Windows Management Framework (WMF) och som ska installeras på den virtuella datorn. Den här egenskapen till senaste installerar den senaste versionen av WMF. För närvarande endast möjliga värden för den här egenskapen är 4.0, 5.0, 5.1 och senaste. Dessa möjliga värden är regleras av uppdateringar. Standardvärdet är **senaste**.

- **Datainsamling**: Anger om tillägget att samla in telemetri. Mer information finns i [Azure DSC-tillägg-datainsamling](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/).

- **Version**: Anger vilken version av DSC-tillägg för att installera. Information om versioner finns [DSC-tillägg versionshistorik](/powershell/dsc/azuredscexthistory).

- **Automatiskt uppgradera delversion**: det här fältet mappar till den **automatisk uppdatering** växla i cmdlets och gör det möjligt att tillägget ska kunna uppdateras automatiskt till den senaste versionen under installationen. **Ja** instruerar hanteraren tillägg du använder den senaste tillgängliga versionen och **nr** tvingar den **Version** angivna installeras. Att välja varken **Ja** eller **nr** är detsamma som att markera **nr**.

## <a name="logs"></a>Logs

Loggar för tillägget lagras på följande plats: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Nästa steg

- Mer information om PowerShell DSC går du till den [PowerShell Dokumentationscenter](/powershell/dsc/overview).
- Granska de [Resource Manager-mall för DSC-tillägg](dsc-template.md).
- Fler funktioner som du kan hantera med hjälp av PowerShell DSC, samt mer DSC-resurser, bläddra i [PowerShell-galleriet](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Mer information om att skicka känslig parametrar i konfigurationer finns [hantera autentiseringsuppgifter på ett säkert sätt med hanteraren för DSC-tillägg](dsc-credentials.md).