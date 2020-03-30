---
title: Aktivera kapslad virtualisering på en virtuell mall-dator i Azure Lab Services (Script) | Microsoft-dokument
description: Lär dig hur du skapar en virtuell mall med flera virtuella datorer inuti.  Med andra ord aktivera kapslad virtualisering på en virtuell mall-dator i Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 56e5ad21f94521565b4df193b2450a1c994b66f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503041"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Aktivera kapslad virtualisering på en virtuell malldator i Azure Lab Services med hjälp av ett skript

Kapslad virtualisering gör att du kan skapa en miljö med flera virtuella datorer i ett labbs virtuella malldator. Publicera mallen kommer att ge varje användare i labbet med en virtuell dator som inrättats med flera virtuella datorer i den.  Mer information om kapslad virtualisering och Azure Lab Services finns i [Aktivera kapslad virtualisering på en virtuell malldator i Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Stegen i den här artikeln fokuserar på att konfigurera kapslad virtualisering för Windows Server 2016 eller Windows Server 2019. Du kommer att använda ett skript för att ställa in malldator med Hyper-V.  Följande steg hjälper dig att använda [Lab Services Hyper-V-skript](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV).

>[!IMPORTANT]
>Välj **Stor (kapslad virtualisering)** eller **Medel (kapslad virtualisering)** för den virtuella datorns storlek när du skapar labbet.  Kapslad virtualisering fungerar inte annars.  

## <a name="run-script"></a>Köra skriptet

1. Om du använder Internet Explorer kan du `https://github.com` behöva lägga till i listan över betrodda platser.
    1. Öppna Internet Explorer.
    1. Välj kugghjulsikonen och välj **Internetalternativ**.  
    1. När dialogrutan **Internetalternativ** visas väljer du **Säkerhet**, väljer **Betrodda platser**och klickar på **Webbplatser.**
    1. När dialogrutan **Betrodda** platser `https://github.com` visas lägger du till i listan över betrodda webbplatser och väljer **Stäng**.

        ![Betrodda platser](../media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Hämta Git-databasfilerna enligt följande steg.
    1. Gå [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/)till .
    1. Klicka på knappen **Klona eller Ladda ned.**
    1. Klicka på **Ladda ner ZIP**.
    1. Extrahera ZIP-filen

    >[!TIP]
    >Du kan också klona [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git)Git-databasen på .

1. Starta **PowerShell** i **administratörsläge.**
1. I PowerShell-fönstret navigerar du till mappen med det nedladdade skriptet. Om du navigerar från den övre mappen i databasfilerna `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`finns skriptet på .
1. Du kan behöva ändra körningsprincipen för att kunna köra skriptet. Kör följande kommando:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Kör skriptet:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Skriptet kan kräva att datorn startas om. Följ instruktionerna från skriptet och kör skriptet igen tills **skriptet har slutförts** i utdata.
1. Glöm inte att återställa körningsprincipen. Kör följande kommando:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Slutsats

Nu är malldatorn redo att skapa virtuella Hyper-V-datorer. Se [Skapa en virtuell dator i Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) för instruktioner om hur du skapar virtuella Hyper-V-datorer. Se även [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/) för att kolla in tillgängliga operativsystem och programvara.  

## <a name="next-steps"></a>Nästa steg

Nästa steg är vanliga för att konfigurera ett labb.

- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Länkar till e-postregistrering till studenter](how-to-configure-student-usage.md#send-invitations-to-users)