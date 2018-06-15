---
title: Felsöka ett problem virtuella Azure-datorn med hjälp av kapslade virtualisering i Azure | Microsoft Docs
description: Så här felsöker du problemet virtuella Azure-datorn med hjälp av kapslade virtualisering i Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/06/2018
ms.author: genli
ms.openlocfilehash: 9026b702e6e0d27817955c70c733bf372005dd4b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31422822"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Felsöka ett problem virtuella Azure-datorn med hjälp av kapslade virtualisering i Azure

Den här artikeln visar hur du skapar en kapslad virtualiseringsmiljö i Microsoft Azure, så du kan montera problemet virtuell disk på Hyper-V-värd (ordningar VM) för felsökning.

## <a name="prerequisites"></a>Förutsättningar

Om du vill montera problemet VM, uppfyller VM ordningar följande krav:

-   Den virtuella datorn ordningar måste vara på samma plats som problemet VM.

-   Den virtuella datorn ordningar måste vara i samma resursgrupp som problemet VM.

-   Den virtuella datorn ordningar måste använda samma typ av Lagringskonto (Standard eller Premium) som problemet VM.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Steg 1: Skapa en virtuell dator ordningar och installera Hyper-V-rollen

1.  Skapa en ny ordningar virtuell dator:

    -  Operativsystem: Windows Server 2016 Datacenter

    -  Storlek: Alla V3-serien med minst två kärnor som stöd för kapslade virtualisering. Mer information finns i [introduktion till den nya Dv3 och Ev3 VM-storleken](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Samma plats, Storage-konto och resursgruppen som problemet VM.

    -  Välj samma lagring som är problemet VM (Standard eller Premium).

2.  När den virtuella datorn ordningar skapas fjärrskrivbord till den Virtuella datorn ordningar.

3.  I Serverhanteraren väljer **hantera** > **Lägg till roller och funktioner**.

4.  I den **installationstyp** väljer **rollbaserad eller funktionsbaserad installation**.

5.  I den **väljer målservern** Kontrollera att den virtuella datorn ordningar är markerad.

6.  Välj den **Hyper-V-rollen** > **Lägg till funktioner**.

7.  Välj **nästa** på den **funktioner** avsnitt.

8.  Om en virtuell växel är tillgängligt, väljer du den. Välj annars **nästa**.

9.  På den **migrering** väljer **nästa**

10. På den **Standardlagringsplatser** väljer **nästa**.

11. Markera kryssrutan om du vill starta om servern automatiskt om det behövs.

12. Välj **installera**.

13. Att installera rollen Hyper-V-servern. Detta tar några minuter och servern startas om automatiskt.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>Steg 2: Skapa problem VM på VM ordningar Hyper-V server

1.  Anteckna namnet på disken i problemet VM och ta sedan bort problemet VM. Kontrollera att du behåller alla anslutna diskar. 

2.  Koppla ditt problem VM OS-disk som en datadisk av ordningar VM.

    1.  När problemet VM tas bort, gå till den Virtuella datorn ordningar.

    2.  Välj **diskar**, och sedan **Lägg till datadisk**.

    3.  Välj VM problemet disken och välj sedan **spara**.

3.  När disken har kopplats bifogade, remote desktop för ordningar VM.

4.  Öppna Diskhantering (diskmgmt.msc). Kontrollera att disken problemets VM är inställd på **Offline**.

5.  Öppna Hyper-V Manager: I **Serverhanteraren**, Välj den **Hyper-V-rollen**. Högerklicka på servern och välj sedan den **Hyper-V Manager**.

6.  Högerklicka på den virtuella datorn ordningar i Hyper-V-hanteraren och välj sedan **ny** > **virtuella** > **nästa**.

7.  Skriv ett namn för den virtuella datorn och välj sedan **nästa**.

8.  Välj **Generation 1**.

9.  Ange startminne på minst 1 024 MB.

10. Välj Hyper-V-nätverksväxel som skapades i tillämpliga fall. Annars flytta till nästa sida.

11. Välj **koppla en virtuell hårddisk senare**.

    ![bilden om Anslut en virtuell hårddisk senare alternativet](./media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Välj **Slutför** när den virtuella datorn skapas.

13. Högerklicka på den virtuella datorn som du skapade och välj sedan **inställningar**.

14. Välj **IDE-styrenhet 0**väljer **hårddisk**, och klicka sedan på **Lägg till**.

    ![bilden om hur du lägger till nya hårddisken](./media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. I **fysisk hårddisk**, markera disken till problemet virtuell dator som du har kopplat till den virtuella Azure-datorn. Om du inte ser alla diskar som anges måste du kontrollera om disken sätts till offline med hjälp av Diskhantering.

    ![bilden om monterar disken](./media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Välj **tillämpa**, och välj sedan **OK**.

18. Dubbelklicka på den virtuella datorn och starta den.

19. Nu kan du arbeta på den virtuella datorn som den lokala virtuella datorn. Du kan följa felsökning du behöver.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Steg 3: Skapa din Azure VM i Azure

1.  När du får den virtuella datorn online igen kan du stänga av den virtuella datorn i Hyper-V manager.

2.  Gå till den [Azure-portalen](https://portal.azure.com) och välj den virtuella datorn ordningar > diskar, kopierar du namnet på disken. Namnet används i nästa steg. Koppla bort fast disk från den virtuella datorn ordningar.

3.  Gå till **alla resurser**, söka efter namnet på disk och välj sedan disken.

     ![bilden om söker disken](./media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Klicka på **skapa VM**.

     ![bilden om hur du skapar virtuella från disken](./media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Du kan också använda Azure PowerShell för att skapa den virtuella datorn från disken. Mer information finns i [Skapa ny virtuell dator från en befintlig disk med hjälp av PowerShell](create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>Nästa steg

Om du har problem med anslutningen till den virtuella datorn finns [felsökning av RDP-anslutningar till en Azure VM](troubleshoot-rdp-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns [felsökning av problem med nätverksanslutningen på en Windows-VM](troubleshoot-app-connection.md).
