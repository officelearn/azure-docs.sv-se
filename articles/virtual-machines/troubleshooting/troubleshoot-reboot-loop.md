---
title: Windows startas om loop på en virtuell Azure-dator | Microsoft Docs
description: Lär dig att felsöka Windows omstartningsloop | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/15/2018
ms.author: genli
ms.openlocfilehash: 032bc1b9c4b1b0e3bf8040ed52bf4db65ba7b6c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318990"
---
# <a name="windows-reboot-loop-on-an-azure-vm"></a>Windows omstartningsloop på en Azure VM
Den här artikeln beskriver omstartningsloop som kan uppstå på en Windows virtuell dator (VM) i Microsoft Azure.

## <a name="symptom"></a>Symtom

När du använder [Startdiagnostik](./boot-diagnostics.md) för att få skärmdumpar av en virtuell dator kan du hitta den virtuella datorn startar men startprocessen komma avbryts och processen startar över.

![Startskärmen 1](./media/troubleshoot-reboot-loop/start-screen-1.png)

## <a name="cause"></a>Orsak

Omstartningsloop inträffar på grund av följande orsaker:

### <a name="cause-1"></a>Orsak 1

Det finns en tredjepartstjänst som har flaggats som är kritiska och den kan inte startas. Detta leder till operativsystemet för att starta om.

### <a name="cause-2"></a>Orsak 2

Vissa ändringar har gjorts i operativsystemet. Vanligtvis är har de att göra en uppdateringsinstallation, programinstallation eller en ny princip. Du kan behöva kontrollera följande loggar för ytterligare information:

- Händelseloggar
- CBS.logWindows
- Update.log

### <a name="cause-3"></a>Orsak 3

Detta kan bero på fel i filsystemet. Dock är det svårt att felsöka och identifiera ändring som orsakar fel i operativsystemet.

## <a name="solution"></a>Lösning

Du löser problemet, [säkerhetskopiera OS-disken](../windows/snapshot-copy-managed-disk.md), och [koppla OS-disken till en undsättning VM](../windows/troubleshoot-recovery-disks-portal.md), följer du lösningsalternativ därefter och testa lösningar i taget.

### <a name="solution-for-cause-1"></a>Lösning för orsak 1

1. När OS-disken är ansluten till en aktiv virtuell dator, se till att disken flaggas som **Online** i Diskhanteringen konsolen och Notera enhetsbeteckningen för den partition som innehåller den **\Windows** mapp.

2. Om disken är inställt på **Offline**, ange den till **Online**.

3. Skapa en kopia av den **\Windows\System32\config** mappen i fall som behövs för en återställning om ändringarna.

4. Öppna Windows Registereditorn (regedit) på Räddade VM.

5. Välj den **HKEY_LOCAL_MACHINE** nyckel och välj sedan **filen** > **Läs in registreringsdatafil** på menyn.

6. Bläddra till filen i den **\Windows\System32\config** mapp.

7. Välj **öppna**, typ **BROKENSYSTEM** namn, expandera den **HKEY_LOCAL_MACHINE** nyckel, och du ser en ytterligare nyckel som heter **BROKENSYSTEM** .

8. Kontrollera vilka ControlSet datorn startas från. Du kan se dess viktiga nummer i följande registernyckel.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\Select\Current`

9. Kontrollera som är i allvarlighetsgrad för VM-agent-tjänsten via följande registernyckel.

    `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\RDAgent\ErrorControl`

10. Om värdet för registernyckeln inte är inställd **2**och gå till nästa minskningen.

11. Om värdet för registernyckeln anges till **2**, ändra värdet från **2** till **1**.

12. Om någon av följande nycklar finns och de har värdet **2** eller **3**, och sedan ändra dessa värden till **1** i enlighet med detta:

    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupCoordinatorSvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupInquirySvc\ErrorControl`
    - `HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet00x\Services\AzureWLBackupPluginSvc\ErrorControl`

13. Välj den **BROKENSYSTEM** nyckel och välj sedan **filen** > **Läs in registreringsdatafil** på menyn.

14. Koppla från den OS-disken från Virtuellt felsökningsdatorn.

15. Ta bort disken från Virtuellt felsökningsdatorn och vänta ungefär 2 minuter för Azure för att frigöra den här disken.

16. [Skapa en ny virtuell dator från OS-disken](../windows/create-vm-specialized.md).

17. Om problemet är löst så du kan behöva installera om den [RDAgent](https://blogs.msdn.microsoft.com/mast/2014/04/07/install-the-vm-agent-on-an-existing-azure-vm/) (WaAppAgent.exe).

### <a name="solution-for-cause-2"></a>Lösning för orsak 2

Återställer den virtuella datorn till den senaste fungerande konfigurationen, följer du stegen i [hur du startar Azure Windows VM med senast fungerande konfiguration](https://support.microsoft.com/help/4016731/).

### <a name="solution-for-cause-3"></a>Lösning för orsak 3

1. När disken är ansluten till en felsökning virtuell dator, se till att disken flaggas som **Online** i konsolen Diskhantering.

2. Skapa en kopia av den **\Windows\System32\config** mappen i fall som behövs för en återställning om ändringarna.

3. Kopiera filerna i den **\Windows\System32\config\regback** mapp och ersätter filer i den **\Windows\System32\config** mapp.

4. Ta bort disken från Virtuellt felsökningsdatorn och vänta ungefär 2 minuter för Azure för att frigöra den här disken.

5. [Skapa en ny virtuell dator från OS-disken](../windows/create-vm-specialized.md).

>[!NOTE]
>Följande procedur bör bara användas som sista resursen. När du återställer från regback kan återställa åtkomst till datorn, anses Operativsystemet inte stabil eftersom det inte finns data som går förlorade i registret mellan tidsstämpel för hive och den aktuella dagen. Du måste skapa en ny virtuell dator och planera att migrera data.
