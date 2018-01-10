---
title: "Hantera dina virtuella datorer med hjälp av Azure PowerShell | Microsoft Docs"
description: "Lär dig kommandon som du kan använda för att automatisera aktiviteter i hantering av virtuella datorer."
services: virtual-machines-windows
documentationcenter: windows
author: singhkays
manager: timlt
editor: 
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
ms.openlocfilehash: 88ab031778957f20dd42955c2c1311d731e2f588
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2018
---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Hantera dina virtuella datorer med Azure PowerShell
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Vanliga PowerShell-kommandon med hjälp av Resource Manager-modellen, se [här](../../virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Många uppgifter du utför varje dag för att hantera dina virtuella datorer kan automatiseras med hjälp av Azure PowerShell-cmdlets. Den här artikeln får du exempel på kommandon för enklare aktiviteter och länkar till artiklar som visar kommandon för mer avancerade aktiviteter.

> [!NOTE]
> Om du inte har installerat och konfigurerat Azure PowerShell men du kan hämta anvisningarna i artikeln [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
> 
> 

## <a name="how-to-use-the-example-commands"></a>Hur du använder kommandona exempel
Du måste ersätta del av texten i kommandona med text som är lämplig för din miljö. Den < och > symbolerna anger du måste ersätta texten. När du ersätter texten, ta bort symbolerna men lämna citattecken på plats.

## <a name="get-a-vm"></a>Hämta en virtuell dator
Detta är en grundläggande uppgift som du ofta använder. Du kan använda den för att hämta information om en virtuell dator, utföra aktiviteter på en virtuell dator eller hämta utdata för att lagra i en variabel.

För att få information om den virtuella datorn kan köra det här kommandot ersätter allt inom citattecken, inklusive den < och > tecken:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Om du vill lagra utdata i en variabel $vm, kör du:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Logga in på en Windows-baserad virtuell dator
Kör följande kommandon:

> [!NOTE]
> Du kan hämta namnet på den virtuella datorn och moln från visningen av den **Get-AzureVM** kommando.
> 
> $svcName = ”<cloud service name>” $vmName = ”<virtual machine name>” $localPath = ”< enhet och mapp plats att lagra nedladdade RDP-filen, exempel: c:\temp >” $localFile = $localPath + ”\" + $vmname +” .rdp ”Get-AzureRemoteDesktopFile - ServiceName $svcName-namnet $vmName - LocalPath $localFile-starta
> 
> 

## <a name="stop-a-vm"></a>Stoppa en virtuell dator
Kör följande kommando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> Använd den här parametern om du vill behålla det virtuella IP (VIP) för Molntjänsten om det är den sista virtuella datorn i Molntjänsten. <br><br> Om du använder parametern StayProvisioned kan faktureras du fortfarande för den virtuella datorn.
> 
> 

## <a name="start-a-vm"></a>Starta en virtuell dator
Kör följande kommando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Anslut en datadisk
Den här uppgiften krävs några steg. Först måste du använda den *** Add-AzureDataDisk *** för att lägga till disken till objektet $vm. Sedan kan du använda **uppdatering AzureVM** att uppdatera konfigurationen av den virtuella datorn.

Du måste också bestämma om du vill koppla en ny disk eller en som innehåller data. För en ny disk kommandot skapar VHD-filen och bifogar den.

Om du vill koppla en ny disk, kör du följande kommando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Om du vill koppla en befintlig disk kör du följande kommando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Om du vill koppla datadiskar från en befintlig VHD-fil i blob storage, kör du kommandot:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Skapa en Windows-baserad virtuell dator
Om du vill skapa en ny Windows-baserad virtuell dator i Azure, följ instruktionerna i [använda Azure PowerShell för att skapa och förkonfigurera Windows-baserade virtuella datorer](create-powershell.md). Det här avsnittet steg du genom att skapa en Azure PowerShell kommandouppsättning som skapar ett Windows-baserade Virtuella kan förkonfigureras:

* Med medlemskap i Active Directory-domän.
* Med ytterligare diskar.
* Anger som en medlem av en befintlig belastningsutjämnad.
* Med en statisk IP-adress.

