---
title: Felsöka en felaktig virtuell azure-dator med hjälp av kapslad virtualisering i Azure | Microsoft-dokument
description: Felsöka ett problem med Azure VM med hjälp av kapslad virtualisering i Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: e1acfc3216ccfaeac035f1ff31e82c7b67c17daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76119626"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>Felsöka en felaktig Virtuell Azure-dator med hjälp av kapslad virtualisering i Azure

Den här artikeln visar hur du skapar en kapslad virtualiseringsmiljö i Microsoft Azure, så att du kan montera disken för den felaktiga virtuella datorn på Hyper-V-värden (Rescue VM) för felsökning.

## <a name="prerequisites"></a>Krav

För att kunna montera den felaktiga virtuella datorn måste den virtuella räddningsdatorn använda samma typ av lagringskonto (Standard eller Premium) som den felaktiga virtuella datorn.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Steg 1: Skapa en virtuell räddnings-VM och installera Hyper-V-roll

1.  Skapa en ny räddnings-VM:

    -  Operativsystem: Windows Server 2016 Datacenter

    -  Storlek: Alla V3-serier med minst två kärnor som stöder kapslad virtualisering. Mer information finns i [Introduktion till de nya storlekarna Dv3 och Ev3 VM](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Samma plats, lagringskonto och resursgrupp som den felaktiga virtuella datorn.

    -  Välj samma lagringstyp som den felaktiga virtuella datorn (Standard eller Premium).

2.  När den virtuella räddningsdatorn har skapats kan fjärrskrivbord till den virtuella räddningsdatorn.

3.  Välj **Hantera** > **Lägg till roller och funktioner i**Serverhanteraren .

4.  I avsnittet **Installationstyp** väljer du **Rollbaserad eller funktionsbaserad installation**.

5.  Kontrollera att den virtuella räddningsdatorn är markerad i avsnittet **Välj målserver.**

6.  Välj **Hyper-V-rollen** > **Lägg till funktioner**.

7.  Välj **Nästa** i avsnittet **Funktioner.**

8.  Om det finns en virtuell växel väljer du den. Annars väljer **du Nästa**.

9.  Välj **Migration** **Nästa**

10. Välj **Nästa**i avsnittet **Standardbutiker** .

11. Markera rutan om du vill starta om servern automatiskt om det behövs.

12. Välj **Installera**.

13. Tillåt att servern installerar Hyper-V-rollen. Detta tar några minuter och servern kommer att starta om automatiskt.

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>Steg 2: Skapa den felaktiga virtuella datorn på den virtuella räddningsdatorns Hyper-V-server

1.  [Skapa en ögonblicksbild disk](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) för OS-disken för den virtuella datorn som har problem, och sedan bifoga ögonblicksbild disk till recuse VM.

2.  Fjärrskrivbord till den virtuella räddningsdatorn.

3.  Öppna Diskhantering (diskmgmt.msc). Kontrollera att disken på den felaktiga virtuella datorn är inställd **på Offline**.

4.  Öppna Hyper-V-hanteraren: Välj **Hyper-V-rollen**i **Serverhanteraren**. Högerklicka på servern och välj sedan **Hyper-V-hanteraren**.

5.  Högerklicka på den virtuella räddningsdatorn i Hyper-V-hanteraren och välj sedan **Ny** > **virtuell dator** > **nästa**.

6.  Skriv ett namn för den virtuella datorn och välj sedan **Nästa**.

7.  Välj **Generation 1**.

8.  Ställ in startminnet på 1024 MB eller mer.

9. Om tillämpligt väljer du hyper-V-nätverksswitchen som skapades. Annars gå till nästa sida.

10. Välj **Bifoga en virtuell hårddisk senare**.

    ![bilden om alternativet Bifoga en virtuell hårddisk senare](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. Välj **Slutför** när den virtuella datorn skapas.

12. Högerklicka på den virtuella datorn som du skapade och välj sedan **Inställningar**.

13. Välj **IDE-styrenhet 0,** välj **Hårddisk**och klicka sedan på **Lägg till**.

    ![bilden om lägger till ny hårddisk](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. I **Fysisk hårddisk**väljer du disken för den felaktiga virtuella datorn som du har kopplat till den virtuella Azure-datorn. Om du inte ser några diskar i listan kontrollerar du om disken är inställd på offline med hjälp av Diskhantering.

    ![bilden om monterar disken](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Tryck på **Tillämpa** och välj sedan **OK**.

16. Dubbelklicka på den virtuella datorn och starta den sedan.

17. Nu kan du arbeta på den virtuella datorn som den lokala virtuella datorn. Du kan följa alla felsökningssteg du behöver.

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>Steg 3: Byt ut os-disken som används av den felaktiga virtuella datorn

1.  När du har säkerhetskopiert den virtuella datorn igen stänger du av den virtuella datorn i Hyper-V-hanteraren.

2.  [Avmontera och koppla loss den reparerade OS-disken](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
).
3.  [Byt ut os-disken som används av den virtuella datorn med den reparerade OS-disken](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>Nästa steg

Om du har problem med att ansluta till den virtuella datorn läser [du Felsöka RDP-anslutningar till en Azure VM](troubleshoot-rdp-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns i [Felsöka problem med programanslutning på en Windows VM](troubleshoot-app-connection.md).
