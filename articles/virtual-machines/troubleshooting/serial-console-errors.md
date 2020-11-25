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
ms.openlocfilehash: cad12a55332a6c7898f9709776c58d7dba8dd81a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022844"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Vanliga fel i Azures serie konsol
Det finns en uppsättning kända fel i Azures serie konsol. Det här är en lista över dessa fel och åtgärder för att minska problemen.

## <a name="common-errors"></a>Vanliga fel

Fel                             |   Åtgärd
:---------------------------------|:--------------------------------------------|
"Azures serie konsol kräver att startdiagnostik är aktiverat. Klicka här för att konfigurera startdiagnostik för den virtuella datorn. " | Se till att [startdiagnostik](boot-diagnostics.md) är aktiverat på den virtuella datorn eller skalnings uppsättningen för den virtuella datorn. Om du använder en serie konsol på en instans av en skalnings uppsättning för virtuella datorer kontrollerar du att din instans har den senaste modellen.
"Azures serie konsol kräver att en virtuell dator körs. Använd Start-knappen ovan för att starta den virtuella datorn. "  | Den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans måste vara i ett startat tillstånd för att få åtkomst till serie konsolen (den virtuella datorn får inte vara stoppad eller frigörs). Se till att den virtuella datorn eller den virtuella datorns skalnings uppsättnings instans körs och försök igen.
"Azures serie konsol är inte aktive rad för den här prenumerationen. kontakta prenumerations administratören för att aktivera." | Azures serie konsol kan inaktive ras på en prenumerations nivå. Om du är prenumerations administratör kan du [Aktivera och inaktivera Azures serie konsol](./serial-console-enable-disable.md). Om du inte är prenumerations administratör bör du kontakta prenumerations administratören för att få nästa steg.
Ett "förbjudet" svar påträffades vid åtkomst till den här VM: s Boot Diagnostic Storage-konto. | Se till att startdiagnostik inte har en konto brand vägg. Det krävs ett tillgängligt lagrings konto för startdiagnostik för att den seriella konsolen ska fungera. Seriell konsol efter design fungerar inte med lagrings kontots brand väggar aktiverade på lagrings kontot för startdiagnostik.
Du har inte de behörigheter som krävs för att använda den här virtuella datorn i serie konsolen. Se till att du har minst roll behörigheter för virtuell dator deltagare.| Åtkomst till serie konsolen kräver att du har åtkomst till deltagar nivå eller mer på den virtuella datorn eller skalnings uppsättningen för virtuella datorer. Mer information finns på [sidan Översikt](serial-console-overview.md).
Det gick inte att hitta lagrings kontot som används för startdiagnostik på den här virtuella datorn. Kontrol lera att startdiagnostik har Aktiver ATS för den här virtuella datorn, att lagrings kontot inte har tagits bort och att du har åtkomst till det här lagrings kontot. | Dubbelkolla att du inte har tagit bort lagrings kontot för startdiagnostik för din virtuella dator eller skalnings uppsättning för virtuella datorer
Den seriella konsolens anslutning till den virtuella datorn påträffade ett fel: "felaktig begäran (400) | Detta kan inträffa om startdiagnostik-URI: n är felaktig. Exempelvis användes "http://" i stället för "https://". Startdiagnostik-URI: n kan åtgärdas med följande kommando: `az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Du har inte de behörigheter som krävs för att skriva till lagrings kontot för startdiagnostik för den här virtuella datorn. Kontrol lera att du har minst behörigheter för VM-deltagare | Seriell konsol åtkomst kräver åtkomst till deltagar nivå på lagrings kontot för startdiagnostik. Mer information finns på [sidan Översikt](serial-console-overview.md).
Det gick inte att fastställa resurs gruppen för startdiagnostikens lagrings konto *&lt; STORAGEACCOUNTNAME &gt;*. Kontrol lera att startdiagnostik har Aktiver ATS för den här virtuella datorn och att du har åtkomst till det här lagrings kontot. | Seriell konsol åtkomst kräver åtkomst till deltagar nivå på lagrings kontot för startdiagnostik. Mer information finns på [sidan Översikt](serial-console-overview.md).
Etableringen av den här virtuella datorn har ännu inte slutförts. Kontrol lera att den virtuella datorn är fullständigt distribuerad och försök att ansluta till serie konsolen igen. | Den virtuella datorn eller skalnings uppsättningen för den virtuella datorn kan fortfarande tillhandahållas. Vänta en stund och försök igen.
Webb-socket är stängt eller kunde inte öppnas. | Du kan behöva lägga till brand Väggs åtkomst till `*.console.azure.com` . En mer detaljerad men längre metod är att tillåta brand Väggs åtkomst till [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653), som ändras relativt regelbundet.
Seriell konsol fungerar inte med ett lagrings konto med hjälp av Azure Data Lake Storage Gen2 med hierarkiska namn områden. | Detta är ett känt problem med hierarkiska namn områden. Du kan undvika detta genom att kontrol lera att lagrings kontot för startdiagnostik för den virtuella datorn inte har skapats med hjälp av Azure Data Lake Storage Gen2. Det här alternativet kan bara anges när lagrings kontot skapas. Du kan behöva skapa ett separat lagrings konto för startdiagnostik utan Azure Data Lake Storage Gen2 aktiverat för att undvika det här problemet.
Den seriella konsolens anslutning till den virtuella datorn påträffade ett fel: "förbjuden" (SubscriptionNotEnabled)-prenumerations namnet är odefinierat, ID: t \<subscription id> har inte aktiverat tillstånd odefinierat | Det här problemet kan inträffa om prenumerationen som en användare har skapat Cloud Shell lagrings konto i har inaktiverats. Du kan åtgärda problemet genom att starta Cloud Shell och [utföra de steg som krävs](../../cloud-shell/persisting-shell-storage.md#unmount-clouddrive-1) för att etablera ett lagrings konto för lagring av Cloud Shell i den aktuella prenumerationen.

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [Azures serie konsol för virtuella Linux-datorer](./serial-console-linux.md)
* Lär dig mer om [Azures serie konsol för virtuella Windows-datorer](./serial-console-windows.md)
