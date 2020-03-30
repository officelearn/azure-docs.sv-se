---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187871"
---
Följ stegen för dina specifika omständigheter.

### <a name="unregister-a-connected-process-server"></a>Avregistrera en ansluten processserver

1. Upprätta en fjärranslutning till processservern som administratör.
2. Öppna Program > **Avinstallera ett program**på **Kontrollpanelen**.
3. Avinstallera programmet **Microsoft Azure Site Recovery Mobility Service/Master Target Server**.
4. Avinstallera programmet **Microsoft Azure Site Recovery Configuration/Process Server**.
5. När programmen i steg 3 och 4 har avinstallerats avinstallerar du **Microsoft Azure Site Recovery Configuration/Process Server Dependencies**.

### <a name="unregister-a-disconnected-process-server"></a>Avregistrera en frånkopplad processserver

Använd bara dessa steg om det inte finns något sätt att återuppliva den maskin där processservern är installerad.

1. Logga in konfigurationsservern som administratör.
2. Öppna en administrativ kommandotolk `%ProgramData%\ASR\home\svsystems\bin`och bläddra till .
3. Kör det här kommandot om du vill hämta en lista över en eller flera processservrar.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Nej: processserverns serienummer.
    - IP/namn: IP-adressen och namnet på den dator som kör processservern.
    - Pulsslag: Senaste pulsslag från processservermaskinen.
    ![Avregistrera-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Ange serienumret för den processserver som du vill avregistrera.
5. Avregistrera en processserver ta bort all information från systemet och visar meddelandet: **Oregistrerat servernamn> (server-IP-adress)**

