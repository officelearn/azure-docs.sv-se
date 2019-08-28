---
title: Felsöka ett problem med en virtuell Azure-dator med hjälp av kapslad virtualisering i Azure | Microsoft Docs
description: Så här felsöker du ett problem med en virtuell Azure-dator med hjälp av kapslad virtualisering i Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 18d7e9b0ab44dfe18df0dcd7cd36fb708649a4bc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089679"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Felsöka ett problem med en virtuell Azure-dator med hjälp av kapslad virtualisering i Azure

Den här artikeln visar hur du skapar en kapslad virtualiseringslösning i Microsoft Azure, så att du kan montera disken för den virtuella datorn med problem på Hyper-V-värden (rädda VM) i fel söknings syfte.

## <a name="prerequisites"></a>Förutsättningar

För att montera den virtuella datorns problem måste den virtuella datorn uppfylla följande krav:

-   Den virtuella räddnings datorn måste finnas på samma plats som det virtuella problemet.

-   Den virtuella räddnings datorn måste finnas i samma resurs grupp som den virtuella datorns problem.

-   Den räddande virtuella datorn måste använda samma typ av lagrings konto (standard eller Premium) som den virtuella datorns problem.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Steg 1: Skapa en virtuell dator för räddning och installera Hyper-V-rollen

1.  Skapa en ny virtuell dator för räddning:

    -  Operativ system: Windows Server 2016 Datacenter

    -  Storlek: Alla v3-serier med minst två kärnor som stöder kapslad virtualisering. Mer information finns i [Introduktion till nya Dv3 och EV3 VM-storlekar](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Samma plats, lagrings konto och resurs grupp som det virtuella problemet.

    -  Välj samma lagrings typ som den virtuella datorn för problemet (standard eller Premium).

2.  När den virtuella räddnings datorn har skapats, fjärr skrivbord till den räddande virtuella datorn.

3.  I Serverhanteraren väljer du **Hantera** > **Lägg till roller och funktioner**.

4.  I avsnittet **Installations typ** väljer du **rollbaserad eller funktions baserad installation**.

5.  I avsnittet **Välj mål server** kontrollerar du att den RÄDDANDE virtuella datorn är markerad.

6.  Välj **rollen Hyper-V-roll** > **Lägg till funktioner**.

7.  Välj **Nästa** i avsnittet **funktioner** .

8.  Om en virtuell växel är tillgänglig väljer du den. Annars väljer du **Nästa**.

9.  I avsnittet **migrering** väljer du **Nästa**

10. I avsnittet **standard butiker** väljer du **Nästa**.

11. Markera kryss rutan om du vill starta om servern automatiskt om det behövs.

12. Välj **Installera**.

13. Tillåt att servern installerar Hyper-V-rollen. Det tar några minuter och servern startas om automatiskt.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>Steg 2: Skapa den virtuella datorns problem på den rädda virtuella datorn Hyper-V-Server

1.  Anteckna namnet på disken i den virtuella datorns problem och ta sedan bort det virtuella problemet. Se till att du behåller alla anslutna diskar. 

2.  Koppla OS-disken till din problem-VM som en data disk för den räddande virtuella datorn.

    1.  När du har tagit bort den virtuella datorns problem går du till den virtuella datorn för räddning.

    2.  Välj **diskar**och sedan **Lägg till data disk**.

    3.  Välj disken för det virtuella problemet och välj sedan **Spara**.

3.  När disken har anslutits till den rädda virtuella datorn via fjärr skrivbord.

4.  Öppna disk hantering (diskmgmt. msc). Kontrol lera att disken för den virtuella datorns problem har angetts till **offline**.

5.  Öppna Hyper-V Manager: I **Serverhanteraren**väljer du **Hyper-V-rollen**. Högerklicka på servern och välj sedan **Hyper-V Manager**.

6.  I Hyper-V Manager högerklickar du på den virtuella datorn för räddning och väljer sedan **ny** > **virtuell dator** > **Nästa**.

7.  Ange ett namn för den virtuella datorn och välj sedan **Nästa**.

8.  Välj **generation 1**.

9.  Ange start minnet på 1024 MB eller mer.

10. Om det är tillämpligt väljer du den Hyper-V-nätverks växel som skapades. Annars går du till nästa sida.

11. Välj **Anslut en virtuell hård disk senare**.

    ![alternativet om alternativet Anslut en virtuell hård disk senare](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Välj **Slutför** när den virtuella datorn skapas.

13. Högerklicka på den virtuella datorn som du skapade och välj sedan **Inställningar**.

14. Välj **IDE-styrenhet 0**, Välj **hård disk**och klicka sedan på **Lägg till**.

    ![bilden om lägger till en ny hård disk](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. På **fysisk hård disk**väljer du disken för den virtuella dator som du har anslutit till den virtuella Azure-datorn. Om du inte ser några diskar i listan kontrollerar du om disken är inställd på offline med hjälp av disk hantering.

    ![avbildningen om att montera disken](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Tryck på **Tillämpa** och välj sedan **OK**.

18. Dubbelklicka på den virtuella datorn och starta den sedan.

19. Nu kan du arbeta med den virtuella datorn som den lokala virtuella datorn. Du kan följa eventuella fel söknings steg som du behöver.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Steg 3: Återskapa din virtuella Azure-dator i Azure

1.  När du har återställt den virtuella datorn online stänger du den virtuella datorn i Hyper-V Manager.

2.  Gå till [Azure Portal](https://portal.azure.com) och välj den virtuella > diskarna för räddnings tjänsten, kopiera namnet på disken. Du kommer att använda namnet i nästa steg. Koppla från den fasta disken från den virtuella datorn för räddning.

3.  Gå till **alla resurser**, Sök efter disk namnet och välj sedan disken.

     ![bilden om sökning på disken](media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Klicka på **Skapa virtuell dator**.

     ![avbildningen om skapar virtuell dator från disken](media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Du kan också använda Azure PowerShell för att skapa den virtuella datorn från disken. Mer information finns i [skapa den nya virtuella datorn från en befintlig disk med hjälp av PowerShell](../windows/create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>Nästa steg

Om du har problem med att ansluta till din virtuella dator kan du läsa [FELSÖKA RDP-anslutningar till en virtuell Azure-dator](troubleshoot-rdp-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns i [Felsöka problem med program anslutningen på en virtuell Windows-dator](troubleshoot-app-connection.md).
