---
title: Det går inte att fjärrans luta till Azure Virtual Machines eftersom NÄTVERKSKORTet är inaktiverat | Microsoft Docs
description: Lär dig hur du felsöker ett problem där RDP Miss lyckas eftersom NÄTVERKSKORTet är inaktiverat på den virtuella Azure-datorn | Microsoft Docs
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
ms.openlocfilehash: 6bce1616ce0c7f7e42810a551acb2f02165ccf93
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078637"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>Det går inte att fjärr skrivbordet till en virtuell dator eftersom nätverks gränssnittet är inaktiverat

Den här artikeln förklarar hur du löser ett problem där du inte kan göra en fjärr skrivbords anslutning till Azure Virtuella Windows-datorer (VM) om nätverks gränssnittet är inaktiverat.


## <a name="symptoms"></a>Symtom

Du kan inte upprätta en RDP-anslutning eller någon annan typ av anslutning till andra portar till en virtuell dator i Azure eftersom nätverks gränssnittet i den virtuella datorn är inaktiverat.

## <a name="solution"></a>Lösning

Innan du följer de här stegen ska du ta en ögonblicks bild av OS-disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).

Om du vill aktivera gränssnittet för den virtuella datorn använder du seriell kontroll eller [Återställ nätverks gränssnitt](#reset-network-interface) för den virtuella datorn.

### <a name="use-serial-control"></a>Använd seriell kontroll

1. Anslut till [serie konsolen och öppna cmd-instansen](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Om serie konsolen inte är aktive rad på den virtuella datorn, se [Återställ nätverks gränssnitt](#reset-network-interface).
2. Kontrol lera nätverks gränssnittets status:

    ```console
    netsh interface show interface
    ```

    Anteckna namnet på det inaktiverade nätverks gränssnittet.

3. Aktivera nätverks gränssnittet:

    ```console
    netsh interface set interface name="interface Name" admin=enabled
    ```

    Om gränssnittet interwork till exempel heter "Ethernet 2" kör du följande kommando:

    ```console
    netsh interface set interface name="Ethernet 2" admin=enabled
    ```

4.  Kontrol lera status för nätverks gränssnittet igen för att se till att nätverks gränssnittet är aktiverat.

    ```console
    netsh interface show interface
    ```

    Du behöver inte starta om den virtuella datorn just nu. Den virtuella datorn kommer att gå att komma åt.

5.  Anslut till den virtuella datorn och se om problemet är löst.

## <a name="reset-network-interface"></a>Återställ nätverks gränssnitt

Ändra IP-adressen till en annan IP-adress som är tillgänglig i under nätet för att återställa nätverks gränssnittet. Det gör du genom att använda Azure Portal eller Azure PowerShell. Mer information finns i [Återställ nätverks gränssnitt](reset-network-interface.md).
