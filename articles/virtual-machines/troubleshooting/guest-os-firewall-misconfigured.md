---
title: Azure VM Guest OS-brandväggen är felkonfigurerad | Microsoft-dokument
description: Lär dig hur du använder serialkonsolen eller offlinemetoden för att diagnostisera och åtgärda en felkonfigurerad gästoperativsystembrandvägg på en fjärrdysto virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: e6f42bdf462ac5261f77bc05c62e50500345fe37
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422538"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Azure VM-gästoperativsystemsbrandväggen är felkonfigurerad

Den här artikeln introducerar hur du åtgärdar felkonfigurerad brandvägg för gästoperativsystem på Azure VM.

## <a name="symptoms"></a>Symtom

1.  Välkomstskärmen för den virtuella datorn (VM) visar att den virtuella datorn är fullständigt inläst.

2.  Beroende på hur gästoperativsystemet är konfigurerat kan det finnas någon eller ingen nätverkstrafik som når den virtuella datorn.

## <a name="cause"></a>Orsak

En felkonfiguration av gästsystembrandväggen kan blockera vissa eller alla typer av nätverkstrafik till den virtuella datorn.

## <a name="solution"></a>Lösning

Innan du följer dessa steg ska du ta en ögonblicksbild av systemdisken för den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [Ögonblicksbild en disk](../windows/snapshot-copy-managed-disk.md).

Om du vill felsöka problemet använder du seriekonsolen eller [reparerar den virtuella datorn offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) genom att koppla systemdisken på den virtuella datorn till en återställnings-VM.

## <a name="online-mitigations"></a>Online mildrande åtgärder

Anslut till [seriekonsolen och öppna sedan en PowerShell-instans](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Om seriekonsolen inte är aktiverad på den virtuella datorn går du till avsnittet "Reparera den virtuella datorn offline" i följande Azure-artikel:

 [Ett internt fel inträffar när du försöker ansluta till en virtuell Azure-dator via Fjärrskrivbord](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

Följande regler kan redigeras för att antingen aktivera åtkomst till den virtuella datorn (via RDP) eller för att ge en enklare felsökningsupplevelse:

*   Fjärrskrivbord (TCP-In): Det här är standardregeln som ger primär åtkomst till den virtuella datorn genom att tillåta RDP i Azure.

*   HTTP-in (Windows Remote Management): Med den här regeln kan du ansluta till den virtuella datorn med hjälp av PowerShell., I Azure kan du med den här typen av åtkomst använda skriptaspekten av fjärrskript och felsökning.

*   Fil- och skrivardelning (SMB-In): Den här regeln aktiverar åtkomst till nätverksresurs som felsökningsalternativ.

*   Fil- och skrivardelning (Ekobegäran - ICMPv4-In): Med den här regeln kan du pinga den virtuella datorn.

I serial console access-instansen kan du fråga om brandväggsregelns aktuella status.

*   Fråga med visningsnamnet som parameter:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Fråga med hjälp av den lokala port som programmet använder:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Fråga med hjälp av den lokala IP-adressen som programmet använder:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Om du ser att regeln är inaktiverad kan du aktivera den genom att köra följande kommando:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   För felsökning kan du stänga av brandväggsprofilerna:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Om du gör detta för att ställa in brandväggen korrekt aktiverar du brandväggen igen när du är klar med felsökningen.

    > [!Note]
    > Du behöver inte starta om den virtuella datorn för att tillämpa den här ändringen.

*   Försök igen för att ansluta till den virtuella datorn via RDP.

### <a name="offline-mitigations"></a>Offline mildrande åtgärder

1.  Information om hur du aktiverar eller inaktiverar brandväggsregler läser du [Aktivera eller inaktivera en brandväggsregel på ett Azure VM Guest OS](enable-disable-firewall-rule-guest-os.md).

2.  Kontrollera om du befinner dig i [gästoperativsystemets brandvägg som blockerar scenariot med inkommande trafik](guest-os-firewall-blocking-inbound-traffic.md).

3.  Om du fortfarande är osäker på om brandväggen blockerar din åtkomst läser du [Inaktivera gäst-OS-brandväggen i Azure VM](disable-guest-os-firewall-windows.md)och aktiverar sedan gästsystembrandväggen igen med rätt regler.

