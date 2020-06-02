---
title: Uppgradera en virtuell Azure-dator med SUSE Linux Enterprise Server till SUSE 15 SP1 | Microsoft Docs
description: Den här artikeln innehåller allmänna anvisningar om hur du använder migrering av SUSE distributions system för att uppgradera SUSE Linux Enterprise Server till SUSE 15 SP1 för en virtuell Azure-dator.
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/21/2020
ms.author: genli
ms.openlocfilehash: 635758ad2624a4fc8b06702f881a34e4b2f3f123
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261109"
---
# <a name="upgrade-azure-vm-with-sles-12-to-sles-15-sp1"></a>Uppgradera virtuell Azure-dator med SLES 12 till SLES 15 SP1

Den här artikeln innehåller allmänna anvisningar om hur du uppgraderar SUSE Linux Enterprise Server (SLES) 12 till SLES 15 SP1 för en virtuell Azure-dator (VM). Mer information finns i [använda migrerings systemet för SUSE distribution](https://documentation.suse.com/suse-distribution-migration-system/1.0/single-html/distribution-migration-system/index.html) och [SUSE Linux Enterprise Server 15 SP1 uppgraderings guide](https://documentation.suse.com/sles/15-SP1/single-html/SLES-upgrade/index.html#sec-update-preparation-update).

## <a name="supported-upgrade-paths"></a>Stödda uppgraderingssökvägar
Den aktuella SLES-versionen måste vara SLES 12 SP4 eller 12 SP5 innan du kan fortsätta till SLES 15 SP1.

![Skärm bilden om uppgraderings Sök väg som stöds](./media/linux-upgrade-suse-15sp1/upgrade-path.png)

## <a name="prerequisites"></a>Krav

- Planera migreringen enligt fönstret godkänt stillestånds tid. Detta beror på att den virtuella datorn startar om under migreringen.
- Ta en fullständig säkerhets kopia av den virtuella datorn innan migreringen.
- Om säkerhets kopiering inte har kon figurer ATS ska du göra en ögonblicks bild säkerhets kopia av OS-disken.
- [Kontrol lera om den virtuella datorn är generation v1 eller generation v2](#check-the-generation-version-for-a-vm).

## <a name="upgrade-from-suse-12-sp4-or-sp5-to-suse-15-sp1"></a>Uppgradera från SUSE 12 SP4 eller SP5 till SUSE 15 SP1

1. Installera det senaste paketet för den virtuella datorn:

    ```
    zypper clean --all
    zypper refresh
    zypper update
    ```

2. När installationen är färdig startar du om den virtuella datorn.

3. Verifiera kernel-och OS-versionen. Kontrol lera att versionen är SUSE 12 SP4 eller SUSE 12 SP5.

    ```
    uname -a
    cat /etc/os-release
    ```

4. Installera **SUSE-migration-sle15-Activation**. När paketet **SUSE-migration-sle15-Activation** installeras, installeras ett annat paket **SLES15-migrering** automatiskt som ett beroende paket. 

   ```
   zypper install suse-migration-sle15-activation
   ```

5. När installationen har slutförts kör du `reboot` kommandot för att starta om den virtuella datorn.

6. Gå till [Azure Portal](https://portal.azure.com), Välj den virtuella datorn och välj sedan **seriell konsol**. Du kommer att se att systemet slutar vid start: starta om systemet. Den här processen bör ta cirka 15-45 minuter. För virtuella datorer i generation 2 kan det vara fastnat på skärmen "starta om: starta om systemet". I det här fallet väntar du i 45 minuter. Om den fortfarande inte fortskrider längre går du till sidan **Översikt** på den virtuella datorn i Azure Portal, stoppar den virtuella datorn och startar sedan om den.

     ![Skärm bilden om meddelanden i serie konsolen](./media/linux-upgrade-suse-15sp1/reboot-message.png)

8. När systemet har startats om med en ny kernel visas följande meddelande.

     ![Skärm bilden om meddelanden i serie konsolen](./media/linux-upgrade-suse-15sp1/output-message.png)
9. Kontrol lera kernel-och OS-versionen för att kontrol lera om systemet har uppgraderats.

    ```
    uname -a
    cat /etc/os-release
    ```

## <a name="check-the-generation-version-for-a-vm"></a>Kontrol lera den virtuella datorns generations version

Du kan använda någon av följande metoder för att kontrol lera generationens version:

- Kör kommandot i SLES-terminalen `dmidecode | grep -i hyper` . Om det är en generation v1 VM, returneras inga utdata. För generationer v2-VM: ar visas följande utdata:

     ![Skärm bilden om utdata för virtuella datorer i generation 2](./media/linux-upgrade-suse-15sp1/output-gen2.png)
- I [Azure Portal](https://portal.azure.com)går du till **Egenskaper** för den virtuella datorn och kontrollerar sedan fältet **VM-generation** .
