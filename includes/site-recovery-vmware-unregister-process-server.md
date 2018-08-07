---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 08/06/2018
ms.author: ramamill
ms.openlocfilehash: 81390d38b4c0c38b7ac6883ae2bf18c64542fa00
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39583026"
---
Anvisningarna för att avregistrera en processerver är olika beroende på dess anslutningsstatus till konfigurationsservern.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Avregistrera en processerver som är i kopplat läge

1. Fjärranslut till processervern som administratör.
2. Starta **Kontrollpanelen** och öppna **Program > Avinstallera ett program**
3. Avinstallera ett program med namnet **Microsoft Azure Site Recovery Configuration/Process Server** (Microsoft Azure Site Recovery konfigurations-/processerver)
4. När steg 3 är slutfört kan du avinstallera **Microsoft Azure Site Recovery Configuration/Process Server Dependencies** (Beroenden för Microsoft Azure Site Recovery konfigurations-/processerver)

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Avregistrera en processerver som är i frånkopplat läge

> [!WARNING]
> Stegen nedan ska användas om det inte finns något sätt att återskapa den virtuella datorn som processervern installerades på.

1. Logga in på din konfigurationsserver som administratör.
2. Öppna en administrativ kommandotolk och gå till katalogen `%ProgramData%\ASR\home\svsystems\bin`.
3. Kör kommandot nu.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Kommandot ovan får listan över processervrar (kan vara mer än en händelse av dubblettposter) seriell number(S.No), IP-adress (IP), namnet på den virtuella datorn på vilken processerver har distribuerats (namn), hjärtat beat för den virtuella datorn (pulsslag) enligt nedan.
    ![Avregistrera cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. Nu kan ange serienumret för den server som processen att avregistrera.
6. Det här information om processervern rensas från systemet och meddelandet: **avregistrerades servernamn > (server-IP-adress)**

