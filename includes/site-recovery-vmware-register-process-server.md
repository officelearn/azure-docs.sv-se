---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: cf39baf34096691144181332566cf567ebc02310
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187870"
---
1. Upprätta en anslutning till fjärrskrivbord på datorn som kör processervern. 
2. Kör cspsconfigtool.exe för att starta konfigurationsverktyget för Processervern för Azure Site Recovery.
    - Verktyget startas automatiskt första gången du loggar in på processervern.
    - Om den inte öppnas automatiskt, klickar du på genvägen på skrivbordet.

3. I **konfigurationsservern FQDN eller IP-** , ange namnet eller IP-adressen för konfigurationsservern som ska registrera processervern.
4. I **Configuration Server-Port**, kontrollera att 443 har angetts. Det här är den port som konfigurationsservern lyssnar efter förfrågningar.
5. I **lösenfrasen för Konfigurationsserveranslutningen**, ange lösenordet som du angav när du ställer in konfigurationsservern. Hitta lösenfrasen:
    -  Gå till Site Recovery-installationsmappen på konfigurationsservern, * *\home\svssystems\bin\** . 
    - Kör det här kommandot: **genpassphrase.exe.n**. Detta visar platsen för lösenfras som du ser sedan.

6. I **Dataöverföringsporten**, lämna standardvärdet såvida inte du har angett en anpassad port.

7. Klicka på **spara** spara inställningarna och registrera processervern.

    
    ![Registrera processervern](./media/site-recovery-vmware-register-process-server/register-ps.png)
