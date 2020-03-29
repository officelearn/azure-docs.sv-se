---
title: Det går inte att fjärransluta till virtuella Azure-datorer eftersom nätverkskortet är inaktiverat | Microsoft-dokument
description: Lär dig hur du felsöker ett problem där RDP misslyckas eftersom nätverkskortet är inaktiverat i Azure VM| Microsoft-dokument
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 315974e4995630eb3af055ac0e1c44f7d8dd0737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918248"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Det går inte att fjärrskrivborda till en virtuell dator eftersom nätverksgränssnittet är inaktiverat

I den här artikeln beskrivs hur du löser ett problem där du inte kan ansluta till Virtuella Azure Windows-datorer om nätverksgränssnittet är inaktiverat.


## <a name="symptoms"></a>Symtom

Du kan inte göra en RDP-anslutning eller någon annan typ av anslutning till andra portar till en virtuell dator i Azure eftersom nätverksgränssnittet i den virtuella datorn är inaktiverat.

## <a name="solution"></a>Lösning

Innan du följer dessa steg ska du ta en ögonblicksbild av OS-disken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).

Om du vill aktivera gränssnittet för den virtuella datorn använder du seriell kontroll eller [återställer nätverksgränssnittet](#reset-network-interface) för den virtuella datorn.

### <a name="use-serial-control"></a>Använd seriell kontroll

1. Anslut till [seriekonsolen och öppna CMD-instansen](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Om seriekonsolen inte är aktiverad på den virtuella datorn läser du [återställa nätverksgränssnittet](#reset-network-interface).
2. Kontrollera nätverksgränssnittets tillstånd:

        netsh interface show interface

    Observera namnet på det inaktiverade nätverksgränssnittet.

3. Aktivera nätverksgränssnittet:

        netsh interface set interface name="interface Name" admin=enabled

    Om gränssnittet mellan arbete till exempel heter "Ethernet 2" kör du följande kommando:

        netsh interface set interface name="Ethernet 2" admin=enabled

4.  Kontrollera nätverksgränssnittets tillstånd igen för att kontrollera att nätverksgränssnittet är aktiverat.

        netsh interface show interface

    Du behöver inte starta om den virtuella datorn just nu. Den virtuella datorn kommer att vara tillbaka nåbar.

5.  Anslut till den virtuella datorn och se om problemet är löst.

## <a name="reset-network-interface"></a>Återställ nätverksgränssnittet

Om du vill återställa nätverksgränssnittet ändrar du IP-adressen till en annan IP-adress som är tillgänglig i undernätet. Det gör du genom att använda Azure-portalen eller Azure PowerShell. Mer information finns i [Återställa nätverksgränssnittet](reset-network-interface.md).
