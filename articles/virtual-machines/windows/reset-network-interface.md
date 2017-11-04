---
title: "Hur du återställer nätverksgränssnittet för virtuell Azure Windows-dator | Microsoft Docs"
description: "Visar hur du återställer nätverksgränssnitt för Virtuella för Windows Azure"
services: virtual-machines-windows, azure-resource-manager
documentationcenter: 
author: genlin
manager: willchen
editor: 
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 77d6bf3bb8b0e03419f93e97746bc1a0a091045e
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Så här återställer du nätverksgränssnitt för Virtuella för Windows Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Du kan inte ansluta till Microsoft Azure Windows virtuell dator (VM) när du inaktiverar standard Network Interface (NIC) eller manuellt anger en statisk IP-adress för nätverkskortet. Den här artikeln visar hur du återställer nätverksgränssnittet för Azure Windows VM, som att lösa problemet med fjärranslutning.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]
## <a name="reset-network-interface"></a>Återställ nätverksgränssnittet

### <a name="for-classic-vms"></a>För klassiska virtuella datorer

Följ dessa steg om du vill återställa nätverksgränssnittet:

1.  Gå till [Azure-portalen]( https://ms.portal.azure.com).
2.  Välj **virtuella datorer (klassisk)**.
3.  Välj den aktuella virtuella datorn.
4.  Välj **IP-adresser**.
5.  Om den **privata IP-tilldelning** är inte **statiska**, ändra den till **statiska**.
6.  Ändra den **IP-adress** till en annan IP-adress som är tillgänglig i undernätet.
7.  Klicka på Spara.
8.  Den virtuella datorn startas om för att initiera ny NIC i systemet.
9.  Försök att RDP till din dator. Om det lyckas, kan du ändra den privata IP-adressen tillbaka till ursprungligt om du vill ha. Annars kan du behålla den. 

### <a name="for-vms-deployed-in-resource-group-model"></a>För virtuella datorer som distribueras i gruppen modell

1.  Gå till [Azure-portalen]( https://ms.portal.azure.com).
2.  Välj den aktuella virtuella datorn.
3.  Välj **nätverksgränssnitt**.
4.  Välj nätverksgränssnitt som är kopplade till datorn
5.  Välj **IP-konfigurationer**.
6.  Välj IP-Adressen. 
7.  Om den **privata IP-tilldelning** är inte **statiska**, ändra den till **statiska**.
8.  Ändra den **IP-adress** till en annan IP-adress som är tillgänglig i undernätet.
9. Den virtuella datorn startas om för att initiera ny NIC i systemet.
10. Försök att RDP till din dator. Om det lyckas, kan du ändra den privata IP-adressen tillbaka till ursprungligt om du vill ha. Annars kan du behålla den. 

## <a name="delete-the-unavailable-nics"></a>Ta bort de tillgängliga nätverkskorten
När du kan fjärrskrivbord till datorn måste du ta bort de gamla nätverkskort om du vill undvika potentiella problem:

1.  Öppna Enhetshanteraren.
2.  Välj **visa** > **Visa dolda enheter**.
3.  Välj **nätverkskort**. 
4.  Kontrollera de nätverkskort som heter som ”Microsoft Hyper-V-nätverkskort”.
5.  Du kan se ett tillgängligt nätverkskort som är nedtonad. Högerklicka på kortet och välj sedan avinstallera.

    ![Bild av nätverkskortet](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Avinstallera endast de tillgängliga nätverkskorten med namnet ”Microsoft Hyper-V-nätverkskort”. Om du avinstallerar någon av de dolda nätverkskorten kan orsaka det ytterligare problem.
    >
    >

6.  Nu ska alla tillgängliga nätverkskort vara avmarkerade från datorn.
