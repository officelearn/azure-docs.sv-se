---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: e18d0a6a01a86f844edc213fc95003cf4f4b46c9
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166128"
---
* Anslut till den virtuella processerverdatorn via Anslutning till fjärrskrivbord.
* Du kan starta cspsconfigtool.exe genom att klicka på genvägen på skrivbordet. (Verktyget startas automatiskt om det första gången du loggar in processervern).
  - Konfigurationsserverns fullständiga domännamn eller IP-adress
  - Porten som konfigurationsservern lyssnar på. Värdet bör vara 443
  - Lösenfras för anslutningen till konfigurationsservern.
  - Dataöverföringsporten som ska konfigureras för den här processervern. Använd standardvärdet, såvida du inte har bytt till en annan port i din miljö.

    ![Registrera processerver](./media/site-recovery-vmware-register-process-server/register-ps.png)
* Klicka på Spara för att spara konfigurationen och registrera processervern.
