---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67534729"
---
1. Upprätta en anslutning till fjärrskrivbord till datorn som kör processservern. 
2. Kör cspsconfigtool.exe för att starta konfigurationsverktyget för Azure Site Recovery Process Server.
    - Verktyget startas automatiskt första gången du loggar in på processservern.
    - Om den inte öppnas automatiskt klickar du på genvägen på skrivbordet.

3. I **Konfigurationsserver FQDN eller IP**anger du namnet eller IP-adressen för den konfigurationsserver som processservern ska registreras med.
4. Kontrollera att 443 har angetts i **Configuration Server Port.** Det här är den port där konfigurationsservern lyssnar efter begäranden.
5. I **Anslutningstillkort**anger du den lösenfras som du angav när du konfigurerade konfigurationsservern. Så här hittar du lösenfrasen:
    -  På konfigurationsservern navigerar du till installationsmappen För platsåterställning **\home\svssystems\bin\**:
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Kör kommandot nedan för att skriva ut den aktuella lösenfrasen:
    ```
    genpassphrase.exe -n
    ```

6. Lämna standardvärdet i **Dataöverföringsporten**om du inte har angett en anpassad port.

7. Klicka på **Spara** spara inställningarna och registrera processservern.

    
    ![Registrera processservern](./media/site-recovery-vmware-register-process-server/register-ps.png)
