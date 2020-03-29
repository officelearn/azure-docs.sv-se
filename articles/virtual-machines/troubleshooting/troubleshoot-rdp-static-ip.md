---
title: Det går inte att fjärrskrivborda till virtuella Azure-datorer på grund av statisk IP| Microsoft-dokument
description: Lär dig hur du felsöker RDP-problem som orsakas av statisk IP i Microsoft Azure.| Microsoft-dokument
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918197"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Det går inte att fjärrskrivbord till Virtuella Azure-datorer på grund av statisk IP

I den hÃ¤r artikeln beskrivs ett problem som gÃ¶r att fJÃ¤rrskrivbord fÃ¶r Azure Windows Virtual Machines (VM) efter att en statisk IP har konfigurerats i den virtuella datorn.


## <a name="symptoms"></a>Symtom

NÃ¤r du ansÃ¤r en RDP-anslutning till en virtuell dator i Azure visas fÃ¶1s fÃ¶ã¶ss:

**Fjärrskrivbord kan inte ansluta till fjärrdatorn av något av följande skäl:**

1. **Fjärråtkomst till servern är inte aktiverad**

2. **Fjärrdatorn är avstängd**

3. **Fjärrdatorn är inte tillgänglig i nätverket**

**Kontrollera att fjärrdatorn är påslagen och ansluten till nätverket och att fjärråtkomst är aktiverad.**

När du kontrollerar skärmbilden i [startdiagnostiken](../troubleshooting/boot-diagnostics.md) i Azure-portalen ser du vm-stövlarna normalt och väntar på autentiseringsuppgifter på inloggningsskärmen.

## <a name="cause"></a>Orsak

Den virtuella datorn har en statisk IP-adress som har definierats i nätverksgränssnittet i Windows. Den här IP-adressen skiljer sig från den adress som har definierats i Azure-portalen.

## <a name="solution"></a>Lösning

Innan du följer dessa steg ska du ta en ögonblicksbild av OS-disken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).

LÃ¶s problemet genom att anse serial control to enable DHCP or [reset network interface](reset-network-interface.md) for the VM.

### <a name="use-serial-control"></a>Använd seriell kontroll

1. Anslut till [seriekonsolen och öppna CMD-instansen](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Om seriekonsolen inte är aktiverad på den virtuella datorn läser du [Återställa nätverksgränssnittet](reset-network-interface.md).
2. Kontrollera om DHCP är inaktiverat i nätverksgränssnittet:

        netsh interface ip show config
3. Om DHCP är inaktiverat återställer du konfigurationen av nätverksgränssnittet så att den använder DHCP:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Om gränssnittet mellan arbetsgränssnittet till exempel namnger "Ethernet 2" kör du följande kommando:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Fråga IP-konfigurationen igen för att kontrollera att nätverksgränssnittet nu är korrekt konfigurerat. Den nya IP-adressen ska matcha den som tillhandahålls av Azure.

        netsh interface ip show config

    Du behöver inte starta om den virtuella datorn just nu. Den virtuella datorn kommer att vara tillbaka nåbar.

Om du sedan vill konfigurera den statiska IP-adressen för den virtuella datorn läser du [Konfigurera statiska IP-adresser för en virtuell dator](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).