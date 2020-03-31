---
title: Windows-omstartsloop på en virtuell Azure-dator | Microsoft-dokument
description: Lär dig hur du felsöker Windows-omstartsloop | Microsoft-dokument
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 3fd0a8bf6bacfec5e2be6dfa52ca51e46c7025f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443589"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Windows-omstartsloop på en virtuell Azure-dator
I den här artikeln beskrivs omstartsloopen som kan uppstå på en virtuell dator (Virtuell Dator) i Microsoft Azure.

## <a name="symptom"></a>Symptom

När du använder [Boot diagnostik](./boot-diagnostics.md) för att få skärmdumpar av en virtuell dator, hittar du den virtuella datorn startar men startprocessen blir avbruten och processen börjar om.

![Startskärm 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Orsak

Omstartsloopen uppstår på grund av följande orsaker:

### <a name="cause-1"></a>Orsak 1

Det finns en tjänst från tredje part som flaggas som kritisk och den kan inte startas. Detta medför att operativsystemet startar om.

### <a name="cause-2"></a>Orsak 2

Vissa ändringar har gjorts i operativsystemet. Vanligtvis är dessa relaterade till en uppdateringsinstallation, programinstallation eller en ny princip. Du kan behöva kontrollera följande loggar för ytterligare information:

- Händelseloggar
- CBS.logFönster
- Update.log

### <a name="cause-3"></a>Orsak 3

Det kan orsaka skador på filsystemet. Det är dock svårt att diagnostisera och identifiera den förändring som orsakar skador på operativsystemet.

## <a name="solution"></a>Lösning

Lös problemet genom [att säkerhetskopiera OS-disken](../windows/snapshot-copy-managed-disk.md)och [ansluta OS-disken till en virtuell räddnings-dator](../windows/troubleshoot-recovery-disks-portal.md)och följ sedan lösningsalternativen därefter, eller prova lösningarna en efter en.

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

1. När OS-disken är ansluten till en fungerande virtuell dator kontrollerar du att disken är flaggad som **online** i diskhanteringskonsolen och noterar enhetsbeteckningen för den partition som innehåller **mappen \Windows.**

2. Om disken är inställd **på Offline**ställer du in den **på Online**.

3. Skapa en kopia av mappen **\Windows\System32\config** om det behövs en återställning av ändringarna.

4. Öppna Windows-registereditorn (regedit) på den virtuella räddningsdatorn.

5. Välj **HKEY_LOCAL_MACHINE** och välj sedan **File** > **Filinläsningsdata från** menyn.

6. Bläddra till SYSTEM-filen i mappen **\Windows\System32\config.**

7. Välj **Öppna,** skriv **BROKENSYSTEM** för namnet, expandera **HKEY_LOCAL_MACHINE** och sedan visas ytterligare en nyckel som heter **BROKENSYSTEM**.

8. Kontrollera vilken ControlSet datorn startar från. Du kommer att se dess nyckelnummer i följande registernyckel.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Kontrollera vilken som är den kritiska vm-agenttjänsten via följande registernyckel.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Om värdet för registernyckeln inte är inställt på **2**går du sedan till nästa begränsning.

11. Om värdet för registernyckeln är inställt på **2**ändrar du värdet från **2** till **1**.

12. Om någon av följande nycklar finns och de har värdet **2** eller **3**och sedan ändra dessa värden till **1** i enlighet med detta:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Välj **BROKENSYSTEM-tangenten** och välj sedan > **Filavlastningsdata från** menyn. **File**

14. Koppla bort OS-disken från den virtuella felsökningsdatorn.

15. Ta bort disken från den virtuella felsökningsdatorn och vänta ca 2 minuter innan Azure släpper den här disken.

16. [Skapa en ny virtuell dator från OS-disken](../windows/create-vm-specialized.md).

17. Om problemet är åtgärdat kan du behöva installera om [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Återställ den virtuella datorn till den senast fungerande konfigurationen, följ stegen i [Starta Azure Windows VM med Senast fungerande konfiguration](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Lösning för orsak 3
>[!NOTE]
>Följande procedur bör endast användas som senaste resurs. Återställning från återskapning från återställning kan återställa åtkomsten till datorn, men operativsystemet anses inte vara stabilt eftersom data går förlorade i registret mellan tidsstämpeln för registreringsdatafilen och den aktuella dagen. Du måste skapa en ny virtuell dator och planera för att migrera data.

1. När disken är ansluten till en felsökande virtuell dator kontrollerar du att disken är flaggad som **online** i diskhanteringskonsolen.

2. Skapa en kopia av mappen **\Windows\System32\config** om det behövs en återställning av ändringarna.

3. Kopiera filerna i mappen **\Windows\System32\config\regback** och ersätt filerna i mappen **\Windows\System32\config.**

4. Ta bort disken från den virtuella felsökningsdatorn och vänta ca 2 minuter innan Azure släpper den här disken.

5. [Skapa en ny virtuell dator från OS-disken](../windows/create-vm-specialized.md).


