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
ms.openlocfilehash: 697f7267437f750bbb751239001145cb1c8af000
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806838"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Vanliga fel i Azures serie konsol
Det finns en uppsättning kända fel i Azures serie konsol. Det här är en lista över dessa fel och åtgärder för att minska problemen.

## <a name="common-errors"></a>Vanliga fel

Fel                             |   Åtgärd
:---------------------------------|:--------------------------------------------|
"Azures serie konsol kräver att startdiagnostik är aktiverat. Klicka här för att konfigurera startdiagnostik för den virtuella datorn. " | Se till att [startdiagnostik](boot-diagnostics.md) är aktiverat på den virtuella datorn eller skalnings uppsättningen för den virtuella datorn. Om du använder en serie konsol på en instans av en skalnings uppsättning för virtuella datorer kontrollerar du att din instans har den senaste modellen.
"Azures serie konsol kräver att en virtuell dator körs. Använd Start-knappen ovan för att starta den virtuella datorn. "  | Den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans måste vara i ett startat tillstånd för att få åtkomst till serie konsolen (den virtuella datorn får inte vara stoppad eller frigörs). Se till att den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans körs och försök igen.
"Azures serie konsol är inte aktive rad för den här prenumerationen. kontakta prenumerations administratören för att aktivera." | Azures serie konsol kan inaktive ras på en prenumerations nivå. Om du är prenumerations administratör kan du [Aktivera och inaktivera Azures serie konsol](./serial-console-enable-disable.md). Om du inte är prenumerations administratör bör du kontakta prenumerations administratören för att få nästa steg.
Ett ”förbjuden”-svar påträffades vid åtkomst till den här Virtuella datorns lagringskonto för startdiagnostik. | Se till att startdiagnostik inte har en konto brand vägg. Ett lagringskonto för tillgänglig startdiagnostik är nödvändiga för seriekonsolen ska fungera. Seriell konsol efter design fungerar inte med lagrings kontots brand väggar aktiverade på lagrings kontot för startdiagnostik.
Du har inte de behörigheter som krävs för att använda den här virtuella datorn i serie konsolen. Kontrollera att du har minst deltagarbehörigheter för virtuell dator.| Åtkomst till serie konsolen kräver att du har åtkomst till deltagar nivå eller mer på den virtuella datorn eller skalnings uppsättningen för virtuella datorer. Mer information finns på [sidan Översikt](serial-console-overview.md).
Det gick inte att hitta lagrings kontot som används för startdiagnostik på den här virtuella datorn. Kontrol lera att startdiagnostik har Aktiver ATS för den här virtuella datorn, att lagrings kontot inte har tagits bort och att du har åtkomst till det här lagrings kontot. | Dubbelkolla att du inte har tagit bort lagrings kontot för startdiagnostik för din virtuella dator eller skalnings uppsättning för virtuella datorer
Den seriella konsolens anslutning till den virtuella datorn påträffade ett fel: "felaktig begäran (400) | Detta kan inträffa om startdiagnostik-URI: n är felaktig. Exempelvis användes "http://" i stället för "https://". Startdiagnostik-URI: n kan åtgärdas med följande kommando: `az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Du har inte de behörigheter som krävs för att skriva till lagrings kontot för startdiagnostik för den här virtuella datorn. Kontrol lera att du har minst behörigheter för VM-deltagare | Seriell konsol åtkomst kräver åtkomst till deltagar nivå på lagrings kontot för startdiagnostik. Mer information finns på [sidan Översikt](serial-console-overview.md).
Det går inte att fastställa resursgruppen för startdiagnostiklagringskonto  *&lt;STORAGEACCOUNTNAME&gt;* . Kontrollera att startdiagnostik har aktiverats för den här virtuella datorn och du har åtkomst till det här lagringskontot. | Seriell konsol åtkomst kräver åtkomst till deltagar nivå på lagrings kontot för startdiagnostik. Mer information finns på [sidan Översikt](serial-console-overview.md).
Etableringen av den här virtuella datorn har ännu inte slutförts. Kontrol lera att den virtuella datorn är fullständigt distribuerad och försök att ansluta till serie konsolen igen. | Den virtuella datorn eller skalnings uppsättningen för den virtuella datorn kan fortfarande tillhandahållas. Vänta en stund och försök igen.
Web socket är stängd eller kunde inte öppnas. | Du kan behöva lägga till brand Väggs åtkomst till `*.console.azure.com`. En mer detaljerad men längre metod är att tillåta brand Väggs åtkomst till [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som ändras relativt regelbundet.
Seriell konsol fungerar inte med ett lagrings konto med hjälp av Azure Data Lake Storage Gen2 med hierarkiska namn områden. | Detta är ett känt problem med hierarkiska namn områden. Du kan undvika detta genom att kontrol lera att lagrings kontot för startdiagnostik för den virtuella datorn inte har skapats med hjälp av Azure Data Lake Storage Gen2. Det här alternativet kan bara anges när lagrings kontot skapas. Du kan behöva skapa ett separat lagrings konto för startdiagnostik utan Azure Data Lake Storage Gen2 aktiverat för att undvika det här problemet.


## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Azures serie konsol för virtuella Linux-datorer](./serial-console-linux.md)
* Lär dig mer om [Azures serie konsol för virtuella Windows-datorer](./serial-console-windows.md)