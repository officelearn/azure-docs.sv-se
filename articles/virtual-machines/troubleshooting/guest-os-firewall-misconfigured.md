---
title: Azure VM gäst-OS-brandväggen är felkonfigurerad | Microsoft Docs
description: ''
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
ms.openlocfilehash: 8f04d943e1db49beed13c183fbd06e401546fc03
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/20/2019
ms.locfileid: "71153896"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Azure VM OS gästbrandvägg är felkonfigurerad

Den här artikeln introducerar hur du åtgärdar felkonfigurerad system-brandväggen på Azure VM för gästoperativsystem.

## <a name="symptoms"></a>Symtom

1.  Välkomstskärmen för virtuell dator (VM) visar att den virtuella datorn har lästs in helt.

2.  Beroende på hur gästoperativsystemet är konfigurerad, kan det vara lite eller ingen nätverkstrafik som når den virtuella datorn.

## <a name="cause"></a>Orsak

En felaktig konfiguration av system gästbrandvägg kan blockera vissa eller alla typer av nätverkstrafik till den virtuella datorn.

## <a name="solution"></a>Lösning

Innan du följer dessa steg kan du ta en ögonblicksbild av systemdisken på den berörda virtuella datorn som en säkerhetskopia. Mer information finns i [ögonblicksbild av en disk](../windows/snapshot-copy-managed-disk.md).

Felsök problemet genom att använda Seriekonsolen eller [reparera den virtuella datorn offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) genom att koppla systemdisken på den virtuella datorn till en virtuell dator för återställning.

## <a name="online-mitigations"></a>Online-åtgärder

Ansluta till den [Seriekonsolen och öppna en PowerShell-instans](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Om Seriekonsolen inte är aktiverad på den virtuella datorn, gå till avsnittet ”reparera den virtuella datorn Offline” i följande Azure-artikel:

 [Ett internt fel inträffar vid försök att ansluta till en Azure-dator via fjärrskrivbord](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

Följande regler kan redigeras för att antingen ge åtkomst till den virtuella datorn (via RDP) eller för att få en enklare felsökning upplevelse:

*   Fjärr skrivbord (TCP-in): Detta är standard regeln som ger primär åtkomst till den virtuella datorn genom att tillåta RDP i Azure.

*   Windows Remote Management (HTTP-in): Med den här regeln kan du ansluta till den virtuella datorn med hjälp av PowerShell. i Azure kan du med den här typen av åtkomst använda skript aspekten för fjärrskriptering och fel sökning.

*   Fil-och skrivar delning (SMB-in): Den här regeln möjliggör åtkomst till nätverks resurser som fel söknings alternativ.

*   Fil-och skrivar delning (ekobegäran-ICMPv4-in): Med den här regeln kan du pinga den virtuella datorn.

Du kan fråga den aktuella statusen för brandväggsregeln i seriell konsolåtkomst-instans.

*   Fråga med hjälp av visningsnamnet som en parameter:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Fråga med hjälp av lokal Port som används i programmet:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Fråga med hjälp av den lokala IP-adressen som används i programmet:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Om du ser att regeln är inaktiverad kan aktivera du det genom att köra följande kommando:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   För felsökning, kan du inaktivera brandväggsprofiler:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Om du gör detta för att ställa in brandväggen korrekt återaktivera brandväggen när du är klar med felsökningen.

    > [!Note]
    > Du behöver inte starta om den virtuella datorn för att tillämpa ändringen.

*   Försök igen att ansluta till den virtuella datorn via RDP.

### <a name="offline-mitigations"></a>Offline-åtgärder

1.  Information om hur du aktiverar eller inaktiverar brand Väggs regler finns i [Aktivera eller inaktivera en brand Väggs regel på en Azure VM gäst operativ system](enable-disable-firewall-rule-guest-os.md).

2.  Kontrollera om du är i den [gäst-OS i brandväggen blockerar inkommande trafik scenariot](guest-os-firewall-blocking-inbound-traffic.md).

3.  Om du använder fortfarande osäker om brandväggen blockerar din åtkomst kan se [inaktivera gäst-OS-brandvägg i Azure VM](disable-guest-os-firewall-windows.md), och sedan återaktivera gästbrandvägg för system med hjälp av rätt regler.

