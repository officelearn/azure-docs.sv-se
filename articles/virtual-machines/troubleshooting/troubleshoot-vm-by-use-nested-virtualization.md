---
title: Felsöka en felande virtuell Azure-dator med hjälp av kapslad virtualisering i Azure | Microsoft Docs
description: Så här felsöker du ett problem med en virtuell Azure-dator med hjälp av kapslad virtualisering i Azure
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
ms.openlocfilehash: f950cb63b5083a85ab5420434abdd9a720115b1a
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94734556"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>Felsöka en skadad virtuell Azure-dator med hjälp av kapslad virtualisering i Azure

Den här artikeln visar hur du skapar en kapslad virtualiseringslösning i Microsoft Azure, så du kan montera disken för den virtuella datorn med fel på Hyper-V-värden (rädda VM) i fel söknings syfte.

## <a name="prerequisites"></a>Förutsättningar

För att kunna montera den felande virtuella datorn måste den rädda virtuella datorn använda samma typ av lagrings konto (standard eller Premium) som den virtuella datorn.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Steg 1: skapa en virtuell dator för räddning och installera Hyper-V-rollen

1.  Skapa en ny virtuell dator för räddning:

    -  Operativ system: Windows Server 2016 Data Center

    -  Storlek: alla v3-serier med minst två kärnor som stöder kapslad virtualisering. Mer information finns i [Introduktion till nya Dv3 och EV3 VM-storlekar](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Samma plats, lagrings konto och resurs grupp som den felande virtuella datorn.

    -  Välj samma lagrings typ som den virtuella datorn för fel (standard eller Premium).

2.  När den virtuella räddnings datorn har skapats, fjärr skrivbord till den räddande virtuella datorn.

3.  I Serverhanteraren väljer du **Hantera**  >  **Lägg till roller och funktioner**.

4.  I avsnittet **Installations typ** väljer du **rollbaserad eller funktions baserad installation**.

5.  I avsnittet **Välj mål server** kontrollerar du att den RÄDDANDE virtuella datorn är markerad.

6.  Välj **rollen Hyper-V-roll**  >  **Lägg till funktioner**.

7.  Välj **Nästa** i avsnittet **funktioner** .

8.  Om en virtuell växel är tillgänglig väljer du den. Annars väljer du **Nästa**.

9.  I avsnittet **migrering** väljer du **Nästa**

10. I avsnittet **standard butiker** väljer du **Nästa**.

11. Markera kryss rutan om du vill starta om servern automatiskt om det behövs.

12. Välj **Installera**.

13. Tillåt att servern installerar Hyper-V-rollen. Det tar några minuter och servern startas om automatiskt.

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>Steg 2: skapa den felande virtuella datorn på den rädda virtuella datorns Hyper-V-Server

1.  [Skapa en ögonblicks bild disk](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) för OS-disken för den virtuella datorn som har problem och Anslut sedan ögonblicks bild disken till den virtuella recuse-datorn.

2.  Fjärr skrivbord till den räddande virtuella datorn.

3.  Öppna disk hantering (diskmgmt. msc). Kontrol lera att disken för den felande virtuella datorn är inställd på **offline**.

4.  Öppna Hyper-V Manager: i **Serverhanteraren** väljer du **Hyper-v-rollen**. Högerklicka på servern och välj sedan **Hyper-V Manager**.

5.  I Hyper-V Manager högerklickar du på den virtuella datorn för räddning och väljer sedan **ny**  >  **virtuell dator**  >  **Nästa**.

6.  Ange ett namn för den virtuella datorn och välj sedan **Nästa**.

7.  Välj **generation 1**.

8.  Ange start minnet på 1024 MB eller mer.

9. Om det är tillämpligt väljer du den Hyper-V-nätverks växel som skapades. Annars går du till nästa sida.

10. Välj **Anslut en virtuell hård disk senare**.

    ![alternativet om alternativet Anslut en virtuell hård disk senare](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. Välj **Slutför** när den virtuella datorn skapas.

12. Högerklicka på den virtuella datorn som du skapade och välj sedan **Inställningar**.

13. Välj **IDE-styrenhet 0**, Välj **hård disk** och klicka sedan på **Lägg till**.

    ![bilden om lägger till en ny hård disk](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. På **fysisk hård disk** väljer du disken för den virtuella dator som du kopplade till den virtuella Azure-datorn. Om du inte ser några diskar i listan kontrollerar du om disken är inställd på offline med hjälp av disk hantering.

    ![avbildningen om att montera disken](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Tryck på **Tillämpa** och välj sedan **OK**.

16. Dubbelklicka på den virtuella datorn och starta den sedan.

17. Nu kan du arbeta med den virtuella datorn som den lokala virtuella datorn. Du kan följa eventuella fel söknings steg som du behöver.

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>Steg 3: Ersätt den OS-disk som används av den virtuella datorn med fel

1.  När du har återställt den virtuella datorn online stänger du den virtuella datorn i Hyper-V Manager.

2.  [Demontera och koppla från den reparerade OS-disken](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-the-original-virtual-hard-disk
).
3.  [Ersätt den OS-disk som används av den virtuella datorn med den reparerade OS-disken](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>Nästa steg

Om du har problem med att ansluta till din virtuella dator kan du läsa [FELSÖKA RDP-anslutningar till en virtuell Azure-dator](troubleshoot-rdp-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns i [Felsöka problem med program anslutningen på en virtuell Windows-dator](troubleshoot-app-connection.md).
