---
title: "Önskad Tillståndkonfiguration för Azure översikt | Microsoft Docs"
description: "Översikt för användning av hanteraren för Microsoft Azure-tillägget för PowerShell Desired State Configuration. Inklusive krav, arkitektur, cmdlets..."
services: virtual-machines-windows
documentationcenter: 
author: mgreenegit
manager: timlt
editor: 
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: ed8b5bc54baa3a5abfb596b202f0af58e1b6c74f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introduktion till Azure Desired State Configuration-tillägg-hanterare

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Azure VM-agenten och dess associerade tillägg är en del av Microsoft Azure Infrastructure Services.
VM-tillägg är programkomponenter som utökar funktionerna för virtuell dator och förenkla olika VM-hanteringsåtgärder.

Primärt användningsfall för önskad tillstånd Configuration Extension är att starta en virtuell dator till den [Azure Automation DSC-tjänsten](../../automation/automation-dsc-overview.md) som ger [fördelar](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig.md#pull-service) inklusive kontinuerlig hantering av VM-konfiguration och integrering med andra operativa verktyg som Azure-övervakning.

Det är också möjligt att använda DSC-tillägg oberoende av Azure Automation DSC-tjänsten, men detta är en enda åtgärd som utförs under distributionen och det finns ingen pågående reporting eller hantering av konfiguration annat än lokalt i den virtuella datorn.

Den här artikeln innehåller information om scenarier, DSC-tillägg för Azure Automation-onboarding och hur du använder DSC-tillägg som ett verktyg för att tilldela konfigurationer av virtuella datorer med hjälp av Azure SDK.

## <a name="prerequisites"></a>Förutsättningar

- **Lokal dator** – att interagera med Virtuella Azure-tillägget måste du använda Azure-portalen eller Azure PowerShell SDK.
- **Gästagenten** -på Azure VM som konfigurerats av DSC-konfigurationen måste vara ett operativsystem som stöder Windows Management Framework (WMF) 4.0 eller senare. En fullständig lista över operativsystemversioner som stöds finns på sidan [DSC-tillägg versionshistorik](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termer och begrepp

Den här handboken förutsätter förtrogenhet med följande:

- **Konfigurationen** -A DSC-konfiguration dokumentet.
- **Noden** -mål för DSC-konfigurationen. I det här dokumentet refererar ”nod” alltid till en Azure VM.
- **Konfigurationsdata** – en .psd1-fil som innehåller miljödata för en konfiguration.

## <a name="architectural-overview"></a>Översikt över arkitekturen

Azure DSC-tillägg använder Azure VM-agenten framework för att leverera, tillämpar och rapportera om DSC-konfigurationer som körs på virtuella Azure-datorer.
DSC-tillägg accepterar ett konfigurationsdokument och en uppsättning parametrar.
Om ingen fil anges, en [standard konfigurationsskript](###default-configuration-script) bäddas med tillägget som används för att ställa in metadata i [Local Configuration Manager](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).

När tillägget anropas för första gången, installeras en version av den Windows Management Framework (WMF) med hjälp av följande logik:

1. Om Azure VM OS är Windows Server 2016, utförs ingen åtgärd. Windows Server 2016 har redan den senaste versionen av PowerShell som är installerad.
2. Om den `wmfVersion` egenskapen har angetts, den versionen av WMF har installerats om det inte är inkompatibel med den Virtuella datorns operativsystem.
3. Om inget `wmfVersion` egenskapen har angetts, den senaste tillämpliga versionen av WMF har installerats.

Installationen av WMF kräver en omstart.
Efter omstart, hämtar filnamnstillägget ZIP-filen som anges i den `modulesUrl` egenskapen om.
Om den här platsen är i Azure blob storage, en SAS-token kan anges i den `sasToken` egenskapen för att få åtkomst till filen.
När .zip hämtas och packat upp, configuration-funktionen anges i `configurationFunction` körs för att generera en MOF-fil.
Tillägget sedan körs `Start-DscConfiguration -Force` med hjälp av genererade MOF-filen.
Tillägget avbildas utdata och skriver den till Azure Status kanalen.

### <a name="default-configuration-script"></a>Standard-konfigurationsskript

Azure DSC-tillägg innehåller en standardkonfiguration skript som är avsedd att användas när onboarding en virtuell dator till Azure Automation DSC-tjänsten.
Skriptparametrarna ligger i linje med konfigurerbara egenskaper för [Local Configuration Manager](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).
Skriptparametrarna är [dokumenteras](extensions-dsc-template.md##default-configuration-script) och fullständig skriptet finns i [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-arm-templates"></a>DSC-tillägg i ARM-mallar

Mallar för distribution av Azure Resource Manager (ARM) är det förväntade sättet att arbeta med DSC-tillägg för de flesta scenarier.
Mer information och exempel på inklusive DSC-tillägg i ARM-mallar för distribution, finns i dedikerade dokumentationssidan [önskade tillstånd Configuration tillägg med Azure Resource Manager-mallar](extensions-dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>PowerShell-Cmdlets för DSC-tillägg

PowerShell-cmdletar för att hantera DSC-tillägg är bäst för interaktiva felsökning och scenarier insamling av information.
Cmdlets kan användas för att paketera, publicera och övervaka distributioner av DSC-tillägg.
Observera att cmdlets för DSC-tillägg inte har ännu har uppdaterats för att fungera med den [standard konfigurationsskript](###default-configuration-script).

`Publish-AzureRMVMDscConfiguration`använder en konfigurationsfil, söker efter beroende DSC-resurser och skapar en .zip-fil som innehåller konfiguration och DSC-resurser som krävs för att införa konfigurationen.
Det kan även skapa paketet lokalt med hjälp av den `-ConfigurationArchivePath` parameter.
Annars publicerar .zip-filen till Azure blob storage och skyddar den med en SAS-token.

ZIP-filen som skapats av denna cmdlet har konfigurationsskript .ps1 i roten på arkivmapp.
Resurser har mappen modulen placeras i arkivmappen.

`Set-AzureRMVMDscExtension`lägger in de inställningar som krävs av PowerShell DSC-tillägg i ett VM-konfigurationsobjekt.

`Get-AzureRMVMDscExtension`Hämtar status för DSC-tillägg för en viss virtuell dator.

`Get-AzureRMVMDscExtensionStatus`Hämtar status för DSC-konfigurationen trätt i kraft av DSC-tillägg-hanteraren.
Den här åtgärden kan utföras på en enda virtuell dator eller grupp med virtuella datorer.

`Remove-AzureRMVMDscExtension`tar bort tillägget hanteraren från en viss virtuell dator.
Denna cmdlet har **inte** ta bort konfigurationen, avinstallera WMF eller ändra inställningarna som används på den virtuella datorn.
Endast hanteraren tillägget tas bort. 

Viktig information om cmdletarna som AzureRM DSC-tillägg:

- Azure Resource Manager-cmdlets är synkrona.
- ResourceGroupName, VMName, ArchiveStorageAccountName, Version och plats alla parametrar är obligatoriska.
- ArchiveResourceGroupName är en valfri parameter. Du kan ange den här parametern när ditt lagringskonto som hör till en annan resursgrupp än den där den virtuella datorn skapas.
- Växeln automatisk uppdatering aktiverar automatisk uppdatering av hanteraren för tillägget till den senaste versionen som och när den är tillgänglig. Obs den här parametern har kan orsaka startas på den virtuella datorn när en ny version av WMF släpps.

### <a name="getting-started-with-cmdlets"></a>Komma igång med Cmdlets

Azure DSC-tillägg kan hantera DSC-konfigurationsdokument direkt för att konfigurera virtuella datorer i Azure under distributionen, även om det inte kommer att registrera noden till Azure Automation så noden kommer **inte*-hanteras centralt.

Ett enkelt exempel på en konfiguration som visas nedan.
Spara den lokalt som ”IisInstall.ps1”:

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

Följande steg placera IisInstall.ps1 skriptet på den angivna virtuella datorn, köra konfigurationen och rapportera om status.

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Azure Portal funktioner

Bläddra till en virtuell dator. Under Inställningar -> Allmänt klickar du på ”Extensions”.
Ett nytt fönster skapas.
Klicka på ”Lägg till” och välj PowerShell DSC.

Portalen måste indata.
**Konfiguration av moduler eller skript**: det här fältet är obligatoriskt (formuläret har inte uppdaterats för den [standard konfigurationsskript](###default-configuration-script).
Kräver en .ps1-fil som innehåller ett konfigurationsskript eller en .zip-fil med en .ps1-konfigurationsskript på roten och alla beroende resurser i modulen mapparna .zip.
Det kan skapas med den `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` cmdlet som ingår i Azure PowerShell SDK.
ZIP-filen har överförts till ditt blob-lagring för användaren som skyddas av en SAS-token.

**Konfigurationsfilen Data PSD1**: det här fältet är valfritt.
Om din konfiguration kräver en konfigurationsfil för data i .psd1 kan använda det här fältet och överföra den till användare blob-lagring där det skyddas av en SAS-token.

**Modulkvalificerade namn Configuration**: .ps1-filer kan ha flera configuration-funktioner.
Ange namnet på .ps1 konfigurationsskript följt av en '\' och namnet på funktionen konfiguration.
Om skriptet .ps1 har namnet ”configuration.ps1” och konfigurationen är ”IisInstall”, skriver du:`configuration.ps1\IisInstall`

**Konfigurationen argument**: om funktionen configuration tar argument, ange dem i här i formatet `argumentName1=value1,argumentName2=value2`.
Observera att det här är ett annat format än hur configuration argument accepteras via PowerShell-cmdlets eller Resource Manager-mallar.

## <a name="logging"></a>Loggning
Loggar placeras i:

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\[Version Number]
```

## <a name="next-steps"></a>Nästa steg
Mer information om PowerShell DSC [finns på PowerShell documentation center](https://msdn.microsoft.com/powershell/dsc/overview). 

Granska de [Azure Resource Manager-mall för DSC-tillägg](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Du hittar ytterligare funktioner som du kan hantera med PowerShell DSC [Bläddra PowerShell-galleriet](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) mer DSC-resurser.

Mer information om skicka känslig parametrar i konfigurationer finns [hantera autentiseringsuppgifter på ett säkert sätt med hanteraren för DSC-tillägg](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
