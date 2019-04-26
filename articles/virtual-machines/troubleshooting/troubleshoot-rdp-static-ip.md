---
title: Det går inte att fjärrskrivbord till Azure Virtual Machines på grund av statisk IP-adress | Microsoft Docs
description: Lär dig att felsöka RDP-problem som orsakas av statisk IP-adress i Microsoft Azure. | Microsoft Docs
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
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 81a3064290e0aa720a4fe6b0fa0d8eb13cfe6903
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318926"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>Det går inte att fjärrskrivbord till Azure Virtual Machines på grund av statisk IP-adress

Den här artikeln beskriver ett problem där det går inte att fjärrskrivbord till Azure Windows Virtual Machines (VM) när en statisk IP-adress har konfigurerats på den virtuella datorn.

> [!NOTE]
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver Resource Manager-distributionsmodellen, som vi rekommenderar att du använder för nya distributioner i stället för den klassiska distributionsmodellen.

## <a name="symptoms"></a>Symtom

När du gör en RDP-anslutning till en virtuell dator i Azure, visas följande felmeddelande visas:

**Fjärrskrivbord kan inte ansluta till fjärrdatorn för något av följande skäl:**

1. **Fjärråtkomst till servern är inte aktiverat**

2. **Fjärrdatorn är avstängd**

3. **Fjärrdatorn är inte tillgänglig i nätverket**

**Kontrollera att fjärrdatorn är påslagen och ansluten till nätverket och att fjärråtkomst har aktiverats.**

När du checkar skärmbilden den [Startdiagnostik](../troubleshooting/boot-diagnostics.md) i Azure-portalen, visas den virtuella datorn startas normalt och väntar på autentiseringsuppgifter i inloggningsskärmen.

## <a name="cause"></a>Orsak

Den virtuella datorn har en statisk IP-adress som har definierats för nätverksgränssnittet i Windows. Den här IP-adress skiljer sig från den adress som har definierats i Azure-portalen.

## <a name="solution"></a>Lösning

Innan du följer dessa steg kan du ta en ögonblicksbild av OS-disken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).

Lös problemet genom att använda seriell kontroll för att aktivera DHCP eller [Återställ nätverksgränssnittet](reset-network-interface.md) för den virtuella datorn.

### <a name="use-serial-control"></a>Använda seriell kontroll

1. Ansluta till [seriella konsolen och öppna CMD instans](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Om Seriekonsolen inte är aktiverad på den virtuella datorn finns i [Återställ nätverksgränssnittet](reset-network-interface.md).
2. Kontrollera om DHCP är inaktiverat i nätverksgränssnittet:

        netsh interface ip show config
3. Om DHCP har inaktiverats kan du återställa konfigurationen av din nätverksgränssnittet för att använda DHCP:

        netsh interface ip set address name="<NIC Name>" source=dhc

    Till exempel gränssnittet interwork namn ”Ethernet 2”, kör du följande kommando:

        netsh interface ip set address name="Ethernet 2" source=dhc

4. Fråga IP-konfigurationen igen för att se till att nätverksgränssnittet är nu korrekt konfigurerad. Den nya IP-adressen ska matcha det som tillhandahålls av Azure.

        netsh interface ip show config

    Du behöver inte starta om den virtuella datorn nu. Den virtuella datorn kommer att tillbaka kan nås.

Efter det om du vill konfigurera den statiska IP-Adressen för den virtuella datorn finns i [konfigurera statiska IP-adresser för en virtuell dator](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md).