---
title: Så här återställer du nätverksgränssnitt för virtuella Azure Windows-datorer | Microsoft Docs
description: Visar hur du återställer nätverksgränssnitt för virtuella Azure Windows-datorer
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: genli
ms.openlocfilehash: 509de7a31f48091374dba52869f6d839fd107312
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47414831"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Så här återställer du nätverksgränssnitt för virtuella Azure Windows-datorer 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Du kan inte ansluta till Microsoft Azure Windows Virtual Machine (VM) när du inaktiverar Standardnätverksgränssnittet (nätverkskort) eller manuellt ställer in en statisk IP-adress för nätverkskortet. Den här artikeln visar hur du återställer nätverksgränssnittet för Azure Windows-dator, vilket löser problemet fjärranslutning.

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]
## <a name="reset-network-interface"></a>Återställ nätverksgränssnittet

### <a name="for-classic-vms"></a>För klassiska virtuella datorer

Följ dessa steg om du vill återställa nätverksgränssnitt:

1.  Gå till [Azure-portalen]( https://ms.portal.azure.com).
2.  Välj **virtuella datorer (klassiska)**.
3.  Välj den berörda virtuella datorn.
4.  Välj **IP-adresser**.
5.  Om den **privat IP-tilldelning** är inte **statiska**, ändra den till **statiska**.
6.  Ändra den **IP-adress** till en annan IP-adress som är tillgänglig i undernätet.
7.  Välj Spara.
8.  Den virtuella datorn startas om för att initiera det nya nätverkskortet i systemet.
9.  Försök att RDP till din dator. Om detta lyckas kan du ändra privat IP-adress tillbaka till ursprungligt om du vill ha. I annat fall kan du behålla den. 

### <a name="for-vms-deployed-in-resource-group-model"></a>För virtuella datorer som distribueras i Resource group-modellen

1.  Gå till [Azure-portalen]( https://ms.portal.azure.com).
2.  Välj den berörda virtuella datorn.
3.  Välj **nätverksgränssnitt**.
4.  Välj nätverksgränssnitt som är associerade med din dator
5.  Välj **IP-konfigurationer**.
6.  Välj IP-Adressen. 
7.  Om den **privat IP-tilldelning** är inte **statiska**, ändra den till **statiska**.
8.  Ändra den **IP-adress** till en annan IP-adress som är tillgänglig i undernätet.
9. Den virtuella datorn startas om för att initiera det nya nätverkskortet i systemet.
10. Försök att RDP till din dator. Om detta lyckas kan du ändra privat IP-adress tillbaka till ursprungligt om du vill ha. I annat fall kan du behålla den. 

## <a name="delete-the-unavailable-nics"></a>Ta bort de tillgängliga nätverkskorten
När du kan fjärrskrivbord till datorn måste du ta bort de gamla nätverkskorten för att undvika potentiella problem:

1.  Öppna Enhetshanteraren.
2.  Välj **visa** > **Visa dolda enheter**.
3.  Välj **nätverkskort**. 
4.  Kontrollera om finns nätverkskort med namnet ”Microsoft Hyper-V-nätverkskort”.
5.  Du kan se ett otillgängligt nätverkskort som är nedtonat. Högerklicka på nätverkskortet och välj sedan avinstallera.

    ![Bild av nätverkskortet](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Endast avinstallera de inte tillgängliga-kort som har namnet ”Microsoft Hyper-V-nätverkskort”. Om du avinstallerar någon av de andra dolt kort, kan det orsaka ytterligare problem.
    >
    >

6.  Nu ska alla otillgängliga nätverkskort vara rensade från systemet.
