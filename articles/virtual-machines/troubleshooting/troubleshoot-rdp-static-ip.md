---
title: Det går inte att fjärrskrivbord till Azure Virtual Machines på grund av statisk IP-adress | Microsoft Docs
description: Lär dig att felsöka RDP-problem som orsakas av statisk IP-adress i Microsoft Azure. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 92ad33fbc759605ae901c3bcf09283c8e0b1c4b5
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918197"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Det går inte att fjärrskrivbord till Azure Virtual Machines på grund av statisk IP-adress

Den här artikeln beskriver ett problem där det går inte att fjärrskrivbord till Azure Windows Virtual Machines (VM) när en statisk IP-adress har konfigurerats på den virtuella datorn.


## <a name="symptoms"></a>Symtom

När du gör en RDP-anslutning till en virtuell dator i Azure, visas följande felmeddelande visas:

**Fjärr skrivbord kan inte ansluta till fjärrdatorn av något av följande skäl:**

1. **Fjärråtkomst till servern är inte aktive rad**

2. **Fjärrdatorn är avstängd**

3. **Fjärrdatorn är inte tillgänglig i nätverket**

**Kontrol lera att fjärrdatorn är påslagen och ansluten till nätverket och att fjärråtkomst har Aktiver ATS.**

När du kontrollerar skärm bilden i [startdiagnostiken](../troubleshooting/boot-diagnostics.md) i Azure Portal ser du att den virtuella datorn startar normalt och väntar på autentiseringsuppgifter på inloggnings skärmen.

## <a name="cause"></a>Orsak

Den virtuella datorn har en statisk IP-adress som har definierats för nätverksgränssnittet i Windows. Den här IP-adress skiljer sig från den adress som har definierats i Azure-portalen.

## <a name="solution"></a>Lösning

Innan du följer dessa steg kan du ta en ögonblicksbild av OS-disken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).

Lös problemet genom att använda seriell kontroll för att aktivera DHCP eller [återställa nätverks gränssnittet](reset-network-interface.md) för den virtuella datorn.

### <a name="use-serial-control"></a>Använda seriell kontroll

1. Anslut till [serie konsolen och öppna cmd-instansen](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Om serie konsolen inte är aktive rad på den virtuella datorn, se [Återställ nätverks gränssnitt](reset-network-interface.md).
2. Kontrollera om DHCP är inaktiverat i nätverksgränssnittet:

        netsh interface ip show config
3. Om DHCP har inaktiverats kan du återställa konfigurationen av din nätverksgränssnittet för att använda DHCP:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Till exempel gränssnittet interwork namn ”Ethernet 2”, kör du följande kommando:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Fråga IP-konfigurationen igen för att se till att nätverksgränssnittet är nu korrekt konfigurerad. Den nya IP-adressen ska matcha det som tillhandahålls av Azure.

        netsh interface ip show config

    Du behöver inte starta om den virtuella datorn nu. Den virtuella datorn kommer att tillbaka kan nås.

Om du vill konfigurera den statiska IP-adressen för den virtuella datorn kan du läsa mer i [Konfigurera statiska IP-adresser för en virtuell dator](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).