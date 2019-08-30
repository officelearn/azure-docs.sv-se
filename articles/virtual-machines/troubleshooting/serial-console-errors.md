---
title: Fel i Azures serie konsol | Microsoft Docs
description: Vanliga fel i Azures serie konsol
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: a997675a2f7a280e8311e2bba7dca21de82a086e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129671"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Vanliga fel i Azures serie konsol
Det finns en uppsättning kända fel i Azures serie konsol. Det här är en lista över dessa fel och åtgärder för att minska problemen.

## <a name="common-errors"></a>Vanliga fel

Fel                            |   Åtgärd
:---------------------------------|:--------------------------------------------|
Det gick inte att hämta inställningarna för startdiagnostik för  *&lt;VMNAME&gt;* . Se till att startdiagnostik har aktiverats för den här virtuella datorn om du vill använda seriekonsolen. ![Fel vid startdiagnostik](./media/virtual-machines-serial-console/virtual-machines-serial-console-boot-diagnostics-error.png) | Se till att startdiagnostik är aktiverat på den virtuella [](boot-diagnostics.md) datorn eller skalnings uppsättningen för den virtuella datorn. Om du använder en serie konsol på en instans av en skalnings uppsättning för virtuella datorer kontrollerar du att din instans har den senaste modellen.
Den virtuella datorn är i tillståndet stoppad frigjord. Starta den virtuella datorn och försök Seriell konsol-anslutning. ![Friallokerat fel](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | Den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans måste vara i ett startat tillstånd för att få åtkomst till serie konsolen. Starta den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans och försök igen.
Ett ”förbjuden”-svar påträffades vid åtkomst till den här Virtuella datorns lagringskonto för startdiagnostik. ![Brand Väggs fel för lagrings konto](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| Se till att startdiagnostik inte har en konto brand vägg. Ett lagringskonto för tillgänglig startdiagnostik är nödvändiga för seriekonsolen ska fungera.
Du har inte de behörigheter som krävs för att använda den här virtuella datorn i serie konsolen. Kontrollera att du har minst deltagarbehörigheter för virtuell dator.| Åtkomst till serie konsolen kräver att du har åtkomst till deltagar nivå eller mer på den virtuella datorn eller skalnings uppsättningen för virtuella datorer. Mer information finns på [sidan Översikt](serial-console-overview.md).
Det gick inte att hitta lagrings kontot som används för startdiagnostik på den här virtuella datorn. Kontrol lera att startdiagnostik har Aktiver ATS för den här virtuella datorn, att lagrings kontot inte har tagits bort och att du har åtkomst till det här lagrings kontot. | Dubbelkolla att du inte har tagit bort lagrings kontot för startdiagnostik för din virtuella dator eller skalnings uppsättning för virtuella datorer
Etableringen av den här virtuella datorn har ännu inte slutförts. Kontrol lera att den virtuella datorn är fullständigt distribuerad och försök att ansluta till serie konsolen igen. | Den virtuella datorn eller skalnings uppsättningen för den virtuella datorn kan fortfarande tillhandahållas. Vänta en stund och försök igen.
Du har inte de behörigheter som krävs för att skriva till lagrings kontot för startdiagnostik för den här virtuella datorn. Kontrol lera att du har minst behörighet för VM-deltagare på. | Seriell konsol åtkomst kräver åtkomst till deltagar nivå på lagrings kontot för startdiagnostik. Mer information finns på [sidan Översikt](serial-console-overview.md).
Det går inte att fastställa resursgruppen för startdiagnostiklagringskonto  *&lt;STORAGEACCOUNTNAME&gt;* . Kontrollera att startdiagnostik har aktiverats för den här virtuella datorn och du har åtkomst till det här lagringskontot. | Seriell konsol åtkomst kräver åtkomst till deltagar nivå på lagrings kontot för startdiagnostik. Mer information finns på [sidan Översikt](serial-console-overview.md).
Web socket är stängd eller kunde inte öppnas. | Du kan behöva lägga till brand Väggs `*.console.azure.com`åtkomst till. En mer detaljerad men längre metod är att tillåta brand Väggs åtkomst till [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som ändras relativt regelbundet.


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Azures serie konsol för virtuella Linux-datorer](./serial-console-linux.md)
* Lär dig mer om [Azures serie konsol för virtuella Windows-datorer](./serial-console-windows.md)