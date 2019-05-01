---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925610"
---
Följ anvisningarna för din specifika omständigheterna.

### <a name="unregister-a-connected-process-server"></a>Avregistrera en ansluten processerver

1. Upprätta en fjärranslutning till processervern som administratör.
2. I den **Kontrollpanelen**öppnar **program > Avinstallera ett program**.
3. Avinstallera programmet **Microsoft Azure Site Recovery Mobility Service/Huvudmålservern**.
4. Avinstallera programmet **Microsoft Azure Site Recovery Configuration/Process Server**.
5. När programmen i steg 3 och 4 avinstalleras, avinstallera **Microsoft Azure Site Recovery Configuration/Process Serverberoenden**.

### <a name="unregister-a-disconnected-process-server"></a>Avregistrera en frånkopplad processerver

Bara använda de här stegen om det inte finns något sätt att återskapa den datorn som processervern är installerad.

1. Logga in konfigurationsservern som en administratör.
2. Öppna en administrativ kommandotolk och gå till `%ProgramData%\ASR\home\svsystems\bin`.
3. Kör detta kommando för att hämta en lista över en eller flera processervrar.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Nej: serienumret process server.
    - IP/Name: IP-adressen och namnet på den dator som kör processervern.
    - Heartbeat: Sista pulsslaget från den process server-datorn.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Ange serienumret för processervern som du vill avregistrera.
5. Avregistrera en processerver ta bort alla dess information från systemet och visar meddelandet: **Avregistrerades servernamn > (server-IP-adress)**

