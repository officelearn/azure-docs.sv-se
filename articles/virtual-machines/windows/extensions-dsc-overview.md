---
title: "Önskad Tillståndkonfiguration för Azure översikt | Microsoft Docs"
description: "Översikt för användning av hanteraren för Microsoft Azure-tillägget för PowerShell Desired State Configuration. Inklusive krav, arkitektur, cmdlets..."
services: virtual-machines-windows
documentationcenter: 
author: zjalexander
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
keywords: 
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 01/09/2017
ms.author: zachal
ms.openlocfilehash: c05c2d541a5f526f362f9cd72fe6d878374112b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introduktion till Azure Desired State Configuration-tillägg-hanterare
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Azure VM-agenten och dess associerade tillägg är en del av Microsoft Azure Infrastructure Services. VM-tillägg är programkomponenter som utökar funktionerna för virtuell dator och förenkla olika VM-hanteringsåtgärder. Till exempel VMAccess-tillägget kan användas för att återställa lösenord för en administratör eller tillägget för anpassat skript som kan användas för att köra ett skript på den virtuella datorn.

Den här artikeln introducerar PowerShell önskad tillstånd Configuration DSC ()-tillägg för virtuella datorer i Azure som en del av Azure PowerShell SDK. Du kan använda nya cmdlet: ar för att överföra och tillämpa en PowerShell DSC-konfiguration på en virtuell Azure-dator aktiveras med PowerShell DSC-tillägg. PowerShell DSC-tillägg anrop till PowerShell DSC till tillämpar mottagna DSC-konfigurationen på den virtuella datorn. Denna funktion är också tillgängliga via Azure-portalen.

## <a name="prerequisites"></a>Krav
**Lokal dator** ska interagera med Virtuella Azure-tillägget, måste du använda Azure-portalen eller Azure PowerShell SDK. 

**Gästagenten** på Azure VM som konfigurerats av DSC-konfigurationen måste vara ett operativsystem som stöder antingen Windows Management Framework (WMF) 4.0 eller 5.0. En fullständig lista över operativsystemversioner som stöds finns på den [DSC-tillägg versionshistorik](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Termer och begrepp
Den här handboken förutsätter förtrogenhet med följande:

Konfiguration – ett dokument för DSC-konfigurationen. 

Nod - mål för DSC-konfigurationen. I det här dokumentet refererar ”nod” alltid till en Azure VM.

Konfigurationsdata - en .psd1 fil som innehåller miljödata för ett konfigurationsobjekt

## <a name="architectural-overview"></a>Översikt över arkitekturen
Azure DSC-tillägg använder Azure VM-agenten framework för att leverera, tillämpar och rapportera om DSC-konfigurationer som körs på virtuella Azure-datorer. DSC-tillägg förväntar sig en .zip-fil som innehåller minst ett konfigurationsdokument och en uppsättning parametrar som tillhandahålls via Azure PowerShell SDK eller Azure-portalen.

När tillägget anropas för första gången, kör installationen. Den här processen installerar en version av den Windows Management Framework (WMF) med hjälp av följande logik:

1. Om Azure VM OS är Windows Server 2016, utförs ingen åtgärd. Windows Server 2016 har redan den senaste versionen av PowerShell som är installerad.
2. Om den `wmfVersion` egenskapen har angetts, den versionen av WMF har installerats om det inte är inkompatibel med den Virtuella datorns operativsystem.
3. Om inget `wmfVersion` egenskapen har angetts, den senaste tillämpliga versionen av WMF har installerats.

Installationen av WMF kräver en omstart. Efter omstart, hämtar filnamnstillägget ZIP-filen som anges i den `modulesUrl` egenskapen. Om den här platsen är i Azure blob storage, en SAS-token kan anges i den `sasToken` egenskapen för att få åtkomst till filen. När .zip hämtas och packat upp, configuration-funktionen anges i `configurationFunction` körs för att generera MOF-filen. Tillägget sedan körs `Start-DscConfiguration -Force` på den genererade MOF-filen. Tillägget samlar in utdata och skriver den tillbaka till Azure Status kanalen. Från och med nu hanterar DSC-MGM övervakning och korrigering som vanligt. 

## <a name="powershell-cmdlets"></a>PowerShell-cmdletar
PowerShell-cmdlets kan användas med Azure Resource Manager eller den klassiska distributionsmodellen att paketera, publicera och övervaka distributioner av DSC-tillägg. Följande cmdlets i listan är modulerna som klassisk distribution, men ”Azure” kan ersättas med ”AzureRm” för att använda Azure Resource Manager-modellen. Till exempel `Publish-AzureVMDscConfiguration` använder den klassiska distributionsmodellen där `Publish-AzureRmVMDscConfiguration` använder Azure Resource Manager. 

`Publish-AzureVMDscConfiguration`använder en konfigurationsfil, söker efter beroende DSC-resurser och skapar en .zip-fil som innehåller konfiguration och DSC-resurser som krävs för att införa konfigurationen. Det kan även skapa paketet lokalt med hjälp av den `-ConfigurationArchivePath` parameter. Annars publicerar .zip-filen till Azure blob storage och skyddar den med en SAS-token.

ZIP-filen som skapats av denna cmdlet har konfigurationsskript .ps1 i roten på arkivmapp. Resurser har mappen modulen placeras i arkivmappen. 

`Set-AzureVMDscExtension`lägger in de inställningar som krävs av PowerShell DSC-tillägg i ett VM-konfigurationsobjekt. I den klassiska distributionsmodellen VM-ändringarna ska tillämpas på en Azure-dator med `Update-AzureVM`. 

`Get-AzureVMDscExtension`Hämtar status för DSC-tillägg för en viss virtuell dator. 

`Get-AzureVMDscExtensionStatus`Hämtar status för DSC-konfigurationen trätt i kraft av DSC-tillägg-hanteraren. Den här åtgärden kan utföras på en enda virtuell dator eller grupp med virtuella datorer.

`Remove-AzureVMDscExtension`tar bort tillägget hanteraren från en viss virtuell dator. Denna cmdlet har **inte** ta bort konfigurationen, avinstallera WMF eller ändra inställningarna som används på den virtuella datorn. Endast hanteraren tillägget tas bort. 

**Viktiga skillnader i ASM och Azure Resource Manager-cmdlets**

* Azure Resource Manager-cmdlets är synkrona. ASM-cmdlets är asynkron.
* ResourceGroupName, VMName, ArchiveStorageAccountName, Version och platsen är alla obligatoriska parametrar i Azure Resource Manager.
* ArchiveResourceGroupName är en ny valfri parameter för Azure Resource Manager. Du kan ange den här parametern när ditt lagringskonto som hör till en annan resursgrupp än den där den virtuella datorn skapas.
* ConfigurationArchive kallas ArchiveBlobName i Azure Resource Manager
* ContainerName kallas ArchiveContainerName i Azure Resource Manager
* StorageEndpointSuffix kallas ArchiveStorageEndpointSuffix i Azure Resource Manager
* Växeln automatisk uppdatering har lagts till Azure Resource Manager för att aktivera automatisk uppdatering av hanteraren för tillägget till den senaste versionen som och när den är tillgänglig. Obs den här parametern har kan orsaka startas på den virtuella datorn när en ny version av WMF släpps. 

## <a name="azure-portal-functionality"></a>Azure portal funktioner
Bläddra till en virtuell dator. Under Inställningar -> Allmänt klickar du på ”Extensions”. Ett nytt fönster skapas. Klicka på ”Lägg till” och välj PowerShell DSC.

Portalen måste indata.
**Konfiguration av moduler eller skript**: det här fältet är obligatoriskt. Kräver en .ps1-fil som innehåller ett konfigurationsskript eller en .zip-fil med en .ps1-konfigurationsskript på roten och alla beroende resurser i modulen mapparna .zip. Det kan skapas med den `Publish-AzureVMDscConfiguration -ConfigurationArchivePath` cmdlet som ingår i Azure PowerShell SDK. ZIP-filen har överförts till ditt blob-lagring för användaren som skyddas av en SAS-token. 

**Konfigurationsfilen Data PSD1**: det här fältet är valfritt. Om din konfiguration kräver en konfigurationsfil för data i .psd1 kan använda det här fältet och överföra den till användare blob-lagring där det skyddas av en SAS-token. 

**Modulkvalificerade namn Configuration**: .ps1-filer kan ha flera configuration-funktioner. Ange namnet på .ps1 konfigurationsskript följt av en '\' och namnet på funktionen konfiguration. Om skriptet .ps1 har namnet ”configuration.ps1” och konfigurationen är ”IisInstall”, skriver du:`configuration.ps1\IisInstall`

**Konfigurationen argument**: om funktionen configuration tar argument, ange dem i här i formatet `argumentName1=value1,argumentName2=value2`. Observera att det här är ett annat format än hur configuration argument accepteras via PowerShell-cmdlets eller Resource Manager-mallar. 

## <a name="getting-started"></a>Komma igång
Azure DSC-tillägg tar i dokument för DSC-konfigurationen och utfärdar dem på Azure Virtual Machines. Ett enkelt exempel på en konfiguration som visas nedan. Spara den lokalt som ”IisInstall.ps1”:

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
###<a name="classic-model"></a>Klassiska modellen
```powershell
#Azure PowerShell cmdlets are required
Import-Module Azure

#Use an existing Azure Virtual Machine, 'DscDemo1'
$demoVM = Get-AzureVM DscDemo1

#Publish the configuration script into user storage.
Publish-AzureVMDscConfiguration -ConfigurationPath ".\IisInstall.ps1" -StorageContext $storageContext -Verbose -Force

#Set the VM to run the DSC configuration
Set-AzureVMDscExtension -VM $demoVM -ConfigurationArchive "IisInstall.ps1.zip" -StorageContext $storageContext -ConfigurationName "IisInstall" -Verbose

#Update the configuration of an Azure Virtual Machine
$demoVM | Update-AzureVM -Verbose

#check on status
Get-AzureVMDscExtensionStatus -VM $demovm -Verbose
```
###<a name="azure-resource-manager-model"></a>Azure Resource Manager-modellen

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script into user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.21 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"

```

## <a name="logging"></a>Loggning
Loggar placeras i:

C:\WindowsAzure\Logs\Plugins\Microsoft.PowerShell.DSC\[versionsnummer]

## <a name="next-steps"></a>Nästa steg
Mer information om PowerShell DSC [finns på PowerShell documentation center](https://msdn.microsoft.com/powershell/dsc/overview). 

Granska de [Azure Resource Manager-mall för DSC-tillägg](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Du hittar ytterligare funktioner som du kan hantera med PowerShell DSC [Bläddra PowerShell-galleriet](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0) mer DSC-resurser.

Mer information om skicka känslig parametrar i konfigurationer finns [hantera autentiseringsuppgifter på ett säkert sätt med hanteraren för DSC-tillägg](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

