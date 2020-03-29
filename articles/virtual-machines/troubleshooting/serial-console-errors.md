---
title: Azure Serial Console-fel | Microsoft-dokument
description: Vanliga fel i Azure Serial Console
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
ms.openlocfilehash: 61ae0ef92fe522a2a038a6076a5e0c0a10ee47b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060695"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Vanliga fel i Azure Serial Console
Det finns en uppsättning kända fel i Azure Serial Console. Det här är en lista över dessa fel och begränsningssteg för dem.

## <a name="common-errors"></a>Vanliga fel

Fel                             |   Åtgärd
:---------------------------------|:--------------------------------------------|
"Azure Serial Console kräver startdiagnostik för att aktiveras. Klicka här för att konfigurera startdiagnostik för din virtuella dator." | Kontrollera att den virtuella datorn eller den virtuella datorns skaluppsättning har [startdiagnostik](boot-diagnostics.md) aktiverad. Om du använder seriell konsol på en skalningsuppsättningsinstans för virtuell dator kontrollerar du att instansen har den senaste modellen.
"Azure Serial Console kräver en virtuell dator som ska köras. Använd Start-knappen ovan för att starta din virtuella dator."  | Den virtuella datorn eller den virtuella datorns skalningsgruppsinstans måste vara i ett startat tillstånd för att komma åt seriekonsolen (den virtuella datorn får inte stoppas eller frigöras). Kontrollera att din vm- eller virtuella datorskalauppsättningsinstans körs och försök igen.
"Azure Serial Console är inte aktiverat för den här prenumerationen, kontakta din prenumerationsadministratör för att aktivera." | Azure Serial Console kan inaktiveras på prenumerationsnivå. Om du är prenumerationsadministratör kan du [aktivera och inaktivera Azure Serial Console](./serial-console-enable-disable.md). Om du inte är prenumerationsadministratör bör du kontakta prenumerationsadministratören för nästa steg.
Ett "Förbjudet" svar påträffades när du kom åt den här virtuella datorns startdiagnostiska lagringskonto. | Kontrollera att startdiagnostik inte har en kontobrandvägg. Ett tillgängligt startdiagnostiskt lagringskonto är nödvändigt för att den seriella konsolen ska fungera. Seriell konsol av design kan inte fungera med lagringskonto brandväggar aktiverade på boot diagnostics lagringskonto.
Du har inte de behörigheter som krävs för att använda den här virtuella datorn med seriekonsolen. Kontrollera att du har minst rollbehörigheter för deltagare i virtuella datorer.| Den seriella konsolåtkomsten kräver att du har åtkomst på deltagarnivå eller högre på den virtuella datorn eller den virtuella datorns skalningsuppsättning. Mer information finns på [översiktssidan](serial-console-overview.md).
Det gick inte att hitta lagringskontot '' som används för startdiagnostik på den här virtuella datorn. Kontrollera att startdiagnostik är aktiverat för den här virtuella datorn, det här lagringskontot har inte tagits bort och du har åtkomst till det här lagringskontot. | Dubbelkolla att du inte har tagit bort lagringskontot för startdiagnostik för din virtuella dator eller skalningsuppsättning för virtuella datorer
Seriell konsolanslutning till den virtuella datorn påträffade ett fel: "Felaktig begäran" (400) | Detta kan inträffa om din startdiagnostik URI är felaktig. Till exempel användes "http://" i stället för "https://". Boot diagnostics URI kan åtgärdas med det här kommandot:`az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Du har inte de behörigheter som krävs för att skriva till lagringskontot för startdiagnostik för den här virtuella datorn. Kontrollera att du har minst vm-deltagarbehörighet | Seriell konsolåtkomst kräver åtkomst på deltagarnivå i lagringskontot för startdiagnostik. Mer information finns på [översiktssidan](serial-console-overview.md).
Det gick inte att fastställa resursgruppen för lagringskontot * &lt;STORAGEACCOUNTNAME&gt;* för startdiagnostik. Kontrollera att startdiagnostik är aktiverat för den här virtuella datorn och du har åtkomst till det här lagringskontot. | Seriell konsolåtkomst kräver åtkomst på deltagarnivå i lagringskontot för startdiagnostik. Mer information finns på [översiktssidan](serial-console-overview.md).
Etableringen för den här virtuella datorn har ännu inte lyckats. Kontrollera att den virtuella datorn är helt distribuerad och försök igen seriell konsolanslutning. | Skaluppsättningen för den virtuella datorn eller den virtuella datorn kan fortfarande etableras. Vänta lite länge och försök igen.
Webbuttaget är stängt eller kunde inte öppnas. | Du kan behöva lägga `*.console.azure.com`till brandväggsåtkomst till . En mer detaljerad men längre metod är att tillåta brandväggsåtkomst till [Microsoft Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som ändras ganska regelbundet.
Seriell konsol fungerar inte med ett lagringskonto med Azure Data Lake Storage Gen2 med hierarkiska namnområden. | Detta är ett känt problem med hierarkiska namnområden. För att minska, se till att din virtuella dators boot diagnostics storage-konto inte skapas med Azure Data Lake Storage Gen2. Det här alternativet kan bara ställas in när lagringskonto skapas. Du kan behöva skapa ett separat startdiagnostiklagringskonto utan Azure Data Lake Storage Gen2 aktiverat för att minska problemet.
Seriell konsolanslutning till den virtuella datorn påträffade ett fel: "Forbidden"(SubscriptionNotEnabled) - Prenumerationsnamn odefinierat, id-prenumerations-ID \<> är i odefinierat tillstånd som inte är aktiverat | Det här problemet kan uppstå om prenumerationen som en användare har skapat sitt Cloud Shell-lagringskonto i har inaktiverats. För att minska, starta Cloud Shell och [utföra de steg som krävs](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#unmount-clouddrive-1) för att återetablera ett säkerhetskopieringslagringskonto för Cloud Shell i den aktuella prenumerationen.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [virtuella Azure-konsol för virtuella Linux-datorer](./serial-console-linux.md)
* Läs mer om [virtuella Azure-seriekonsolen för Virtuella Datorer i Windows](./serial-console-windows.md)