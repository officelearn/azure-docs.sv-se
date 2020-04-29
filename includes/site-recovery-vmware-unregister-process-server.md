---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "67187871"
---
Följ anvisningarna för dina särskilda omständigheter.

### <a name="unregister-a-connected-process-server"></a>Avregistrera en ansluten processerver

1. Upprätta en fjärr anslutning till processervern som administratör.
2. Öppna program på **kontroll panelen**och **> Avinstallera ett program**.
3. Avinstallera programmet **Microsoft Azure Site Recovery mobilitets tjänsten/huvud mål servern**.
4. Avinstallera programmet **Microsoft Azure Site Recovery konfiguration/processerver**.
5. När programmen i steg 3 och 4 har avinstallerats avinstallerar du **Microsoft Azure Site Recovery konfiguration/process Server beroenden**.

### <a name="unregister-a-disconnected-process-server"></a>Avregistrera en frånkopplad processerver

Använd bara de här stegen om det inte finns något sätt att återskapa den dator där processervern är installerad.

1. Logga in på konfigurations servern som administratör.
2. Öppna en administrativ kommando tolk och bläddra till `%ProgramData%\ASR\home\svsystems\bin`.
3. Kör det här kommandot för att hämta en lista över en eller flera process servrar.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Nej: process serverns serie nummer.
    - IP/namn: IP-adressen och namnet på den dator som kör processervern.
    - Pulsslag: senaste pulsslag från processervern.
    ![Avregistrera-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Ange serie numret för den processerver som du vill avregistrera.
5. När du avregistrerar en processerver tas all information bort från systemet och meddelandet visas: det **gick inte att registrera Server namnet> (Server-IP-adress)**

