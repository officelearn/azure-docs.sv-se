---
title: Felsöka ett problem virtuell Azure-dator med hjälp av kapslad virtualisering i Azure | Microsoft Docs
description: Så här felsöker du problem virtuell Azure-dator med hjälp av kapslad virtualisering i Azure
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
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: c84d015da907c8792f09d1d60e6bc8eddb7e2957
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444367"
---
# <a name="troubleshoot-a-problem-azure-vm-by-using-nested-virtualization-in-azure"></a>Felsöka ett problem virtuell Azure-dator med hjälp av kapslad virtualisering i Azure

Den här artikeln visar hur du skapar en miljö för kapslad virtualisering i Microsoft Azure, så att du kan montera disken om problemet virtuell dator på Hyper-V-värd (Räddade VM) i felsökningssyfte.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill montera problemet VM måste Räddade VM uppfylla följande krav:

-   Den virtuella datorn Räddade måste vara på samma plats som problemet VM.

-   Den virtuella datorn Räddade måste finnas i samma resursgrupp som problemet VM.

-   Den virtuella datorn Räddade måste använda samma typ av Lagringskonto (Standard eller Premium) som problemet VM.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Steg 1: Skapa en Räddade virtuell dator och installera Hyper-V-rollen

1.  Skapa en ny Räddade virtuell dator:

    -  Operativsystem: Windows Server 2016 Datacenter

    -  Storlek: Alla V3-serien med minst två kärnor som har stöd för kapslad virtualisering. Mer information finns i [introduktion till de nya storlekarna Dv3 och Ev3 för Virtuella](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Samma plats, Lagringskontot och resursgrupp som problemet VM.

    -  Välj samma lagringstyp som problemet VM (Standard eller Premium).

2.  Efter den Räddade virtuella datorn skapas, fjärrskrivbord till den virtuella datorn Räddade.

3.  I Serverhanteraren väljer **hantera** > **Lägg till roller och funktioner**.

4.  I den **installationstyp** väljer **rollbaserad eller funktionsbaserad installation**.

5.  I den **väljer målservern** Kontrollera att den virtuella datorn Räddade har valts.

6.  Välj den **Hyper-V-rollen** > **Lägg till funktioner**.

7.  Välj **nästa** på den **funktioner** avsnittet.

8.  Om en virtuell växel är tillgänglig väljer du den. Välj annars **nästa**.

9.  På den **migrering** väljer **nästa**

10. På den **Standardlagringsplatser** väljer **nästa**.

11. Markera rutan om du vill starta om servern automatiskt om det behövs.

12. Välj **Installera**.

13. Tillåt att servern installera Hyper-V-rollen. Detta tar några minuter och servern startas om automatiskt.

## <a name="step-2-create-the-problem-vm-on-the-rescue-vms-hyper-v-server"></a>Steg 2: Skapa problemet VM på den Räddade Virtuella Hyper-V-servern

1.  Anteckna namnet på disken i problemet VM och ta sedan bort problemet VM. Se till att du behåller alla anslutna diskar. 

2.  Koppla ditt problem VM OS-disk som en datadisk på den virtuella datorn Räddade.

    1.  När du har problem med virtuella datorn tas bort, gå till den virtuella datorn Räddade.

    2.  Välj **diskar**, och sedan **Lägg till datadisk**.

    3.  Välj problem-virtuell disk och välj sedan **spara**.

3.  När disken har kopplats anslutna, remote desktop för Räddade VM.

4.  Öppna Diskhantering (diskmgmt.msc). Kontrollera att disken problemets VM är inställd på **Offline**.

5.  Öppna Hyper-V Manager: I **Serverhanteraren**väljer den **Hyper-V-rollen**. Högerklicka på servern och välj sedan den **Hyper-V Manager**.

6.  Högerklicka på Räddade virtuell dator i Hyper-V-hanteraren och välj sedan **New** > **VM** > **nästa**.

7.  Skriv ett namn för den virtuella datorn och välj sedan **nästa**.

8.  Välj **Generation 1**.

9.  Ange startminne på minst 1 024 MB.

10. Välj Hyper-V-nätverksväxel som skapades om tillämpligt. Annars gå till nästa sida.

11. Välj **koppla en virtuell hårddisk senare**.

    ![bild som visar Anslut en virtuell hårddisk senare alternativet](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

12. Välj **Slutför** när den virtuella datorn skapas.

13. Högerklicka på den virtuella datorn som du skapade och välj sedan **inställningar**.

14. Välj **IDE-styrenhet 0**väljer **hårddisk**, och klicka sedan på **Lägg till**.

    ![bild som visar lägger till ny hårddisk](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

15. I **fysisk hårddisk**, Välj disken för problemet virtuell dator som du har kopplat till den virtuella Azure-datorn. Om du inte ser alla diskar som visas, kontrollerar du om disken sätts till offline med hjälp av Diskhantering.

    ![bild som visar monterar disken](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


17. Tryck på **Tillämpa** och välj sedan **OK**.

18. Dubbelklicka på den virtuella datorn och starta den.

19. Nu kan du arbeta på den virtuella datorn som den lokala virtuella datorn. Du kan följa eventuella felsökningssteg som du behöver.

## <a name="step-3-re-create-your-azure-vm-in-azure"></a>Steg 3: Återskapa din Azure-dator i Azure

1.  När du får den virtuella datorn online igen kan du stänga av den virtuella datorn i Hyper-V manager.

2.  Gå till den [Azure-portalen](https://portal.azure.com) och väljer den virtuella datorn Räddade > diskar, kopierar du namnet på disken. Du använder namnet i nästa steg. Koppla bort fast disk från den virtuella datorn Räddade.

3.  Gå till **alla resurser**, Sök efter namnet på disken och sedan väljer du disken.

     ![bild som visar söker disken](media/troubleshoot-vm-by-use-nested-virtualization/search-disk.png)     

4. Klicka på **Skapa virtuell dator**.

     ![bild som visar skapar virtuell dator från disken](media/troubleshoot-vm-by-use-nested-virtualization/create-vm-from-vhd.png) 

Du kan också använda Azure PowerShell för att skapa den virtuella datorn från disken. Mer information finns i [skapa den nya virtuella datorn från en befintlig disk med hjälp av PowerShell](../windows/create-vm-specialized.md#create-the-new-vm). 

## <a name="next-steps"></a>Nästa steg

Om du har problem med att ansluta till den virtuella datorn kan du läsa [felsöka RDP-anslutningar till en Azure VM](troubleshoot-rdp-connection.md). Problem med att komma åt program som körs på den virtuella datorn finns [felsöka problem med programanslutningar på en virtuell Windows-dator](troubleshoot-app-connection.md).
