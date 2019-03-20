---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0d090f43b69b42a07f1c8949d1662e8e720f3cf4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908543"
---
Anvisningarna för att avregistrera en processerver är olika beroende på dess anslutningsstatus till konfigurationsservern.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Avregistrera en processerver som är i kopplat läge

1. Fjärranslut till processervern som administratör.
2. Starta den **Kontrollpanelen** och öppna **program > Avinstallera ett program**.
3. Avinstallera ett program med namnet **Microsoft Azure Site Recovery Mobility Service/Huvudmålservern**.
4. Avinstallera ett program med namnet **Microsoft Azure Site Recovery Configuration/Process Server**.
5. När programmen i steg 3 och 4 avinstalleras, du kan avinstallera **Microsoft Azure Site Recovery Configuration/Process Serverberoenden**.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Avregistrera en processerver som är i frånkopplat läge

> [!WARNING]
> Använd den nedanstående steg om det inte finns något sätt att återskapa den virtuella datorn som Processervern installerades.

1. Logga in på din konfigurationsserver som administratör.
2. Öppna en administrativ kommandotolk och gå till katalogen `%ProgramData%\ASR\home\svsystems\bin`.
3. Kör kommandot nu.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Kommandot ovan får listan över processervrar (kan vara mer än en händelse av dubblettposter) seriell number(S.No), IP-adress (IP), namnet på den virtuella datorn på vilken processerver har distribuerats (namn), hjärtat beat för den virtuella datorn (pulsslag) enligt nedan.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. Nu kan ange serienumret för den server som processen att avregistrera.
6. Det här information om processervern rensas från systemet och meddelandet: **Avregistrerades servernamn > (server-IP-adress)**

