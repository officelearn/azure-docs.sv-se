---
title: Hantera dina virtuella datorer med hjälp av Azure PowerShell | Microsoft Docs
description: Lär dig kommandon som du kan använda för att automatisera uppgifter i hanteringen av dina virtuella datorer.
services: virtual-machines-windows
documentationcenter: windows
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 7cdf9bd3-6578-4069-8a95-e8585f04a394
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2016
ms.author: kasing
ms.openlocfilehash: b7fafa148417ba1667ec0277b414105f95e428ce
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971793"
---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Hantera dina virtuella datorer med Azure PowerShell
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Vanliga PowerShell-kommandon med hjälp av Resource Manager finns i [här](../../virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Många aktiviteter som du gör varje dag för att hantera dina virtuella datorer kan automatiseras med hjälp av Azure PowerShell-cmdlets. Den här artikeln får du exempel på kommandon för enklare uppgifter och länkar till artiklar som visar kommandon för mer avancerade aktiviteter.

> [!NOTE]
> Om du inte har installerat och konfigurerat Azure PowerShell ännu, du kan hämta anvisningarna i artikeln [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
> 
> 

## <a name="how-to-use-the-example-commands"></a>Hur du använder kommandona i exempel
Du måste ersätta del av texten i kommandona med text som är lämplig för din miljö. Den < och > symbolerna anger du måste ersätta texten. När du ersätter texten, ta bort symbolerna men lämna citattecken på plats.

## <a name="get-a-vm"></a>Hämta en virtuell dator
Det här är en grundläggande uppgift som du använder ofta. Du kan använda den för att få information om en virtuell dator, genomföra åtgärder på en virtuell dator eller få ett resultat som lagrar i en variabel.

För att få information om den virtuella datorn kör detta kommando och Ersätt allt inom citattecken, inklusive den < och > tecken:

    Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

För att lagra utdata i variabeln $vm, kör du:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Logga in på en Windows-baserad virtuell dator
Kör följande kommandon:

> [!NOTE]
> Du kan hämta tjänstnamn för virtuell dator och molnet från visningen av de **Get-AzureVM** kommando.
> 
> $svcName = `"<cloud service name>"` $vmName = `"<virtual machine name>"` $localPath = `"<drive and folder location to store the downloaded RDP file, example: c:\temp >"` $localFile = $localPath + ”\" + $vmname +” RDP ”get-AzureRemoteDesktopFile - ServiceName $svcName-och namnge $vmName - LocalPath $localFile-starta
> 
> 

## <a name="stop-a-vm"></a>Stoppa en virtuell dator
Kör följande kommando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> Använd den här parametern för att lagra den virtuella IP (VIP) för Molntjänsten om det är den sista virtuella datorn i Molntjänsten. <br><br> Om du använder parametern StayProvisioned, kommer du fortfarande att faktureras för den virtuella datorn.
> 
> 

## <a name="start-a-vm"></a>Starta en virtuell dator
Kör följande kommando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Anslut en datadisk
Den här uppgiften kräver några få steg. Först måste du använda den ****Add-AzureDataDisk**** för att lägga till disken till objektet $vm. Sedan använder du **Update-AzureVM** cmdlet för att uppdatera konfigurationen av den virtuella datorn.

Du måste också bestämma om du vill koppla en ny disk eller en som innehåller data. För en ny disk kommandot skapar VHD-filen och kopplar den.

Om du vill koppla en ny disk, kör du följande kommando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Om du vill koppla en befintlig disk kör du följande kommando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Om du vill koppla datadiskar från en befintlig .vhd-fil i blob storage, kör du följande kommando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Skapa en Windows-baserad virtuell dator
Om du vill skapa en ny Windows-baserad virtuell dator i Azure, följ instruktionerna i [använder Azure PowerShell för att skapa och förkonfigurera en Windows-baserade virtuella datorer](create-powershell.md). Den här artikeln beskriver du genom att skapa en Azure PowerShell-session som angiven med kommando skapar en Windows-baserad virtuell dator som kan förkonfigureras:

* Med Active Directory medlemskapet i domänen.
* Med ytterligare diskar.
* Ställ in som medlem i en befintlig Utjämning av nätverksbelastning.
* Med en statisk IP-adress.

