---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67534729"
---
1. Upprätta en anslutning till fjärrskrivbord på datorn som kör processervern. 
2. Kör cspsconfigtool.exe för att starta konfigurationsverktyget för Processervern för Azure Site Recovery.
    - Verktyget startas automatiskt första gången du loggar in på processervern.
    - Om den inte öppnas automatiskt, klickar du på genvägen på skrivbordet.

3. I **konfigurationsservern FQDN eller IP-** , ange namnet eller IP-adressen för konfigurationsservern som ska registrera processervern.
4. I **Configuration Server-Port**, kontrollera att 443 har angetts. Det här är den port som konfigurationsservern lyssnar efter förfrågningar.
5. I **lösenfrasen för Konfigurationsserveranslutningen**, ange lösenordet som du angav när du ställer in konfigurationsservern. Hitta lösenfrasen:
    -  Gå till Site Recovery-installationsmappen på konfigurationsservern, * *\home\svssystems\bin\** :
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Kör den kommandot att skriva ut den aktuella lösenfrasen nedan:
    ```
    genpassphrase.exe -n
    ```

6. I **Dataöverföringsporten**, lämna standardvärdet såvida inte du har angett en anpassad port.

7. Klicka på **spara** spara inställningarna och registrera processervern.

    
    ![Registrera processervern](./media/site-recovery-vmware-register-process-server/register-ps.png)
