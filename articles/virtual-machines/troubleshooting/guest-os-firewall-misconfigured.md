---
title: Azures gäst-OS-brandvägg är felkonfigurerad | Microsoft Docs
description: Lär dig hur du använder serie konsolen eller offline-metoden för att diagnostisera och åtgärda en felkonfigurerad brand vägg för gäst operativ system på en virtuell Azure-dator.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80422538"
---
# <a name="azure-vm-guest-os-firewall-is-misconfigured"></a>Azures gäst-OS-brandvägg för virtuella datorer är felkonfigurerad

Den här artikeln beskriver hur du åtgärdar en felkonfigurerad brand vägg för gäst operativ system på en virtuell Azure-dator.

## <a name="symptoms"></a>Symtom

1.  Välkomst skärmen för den virtuella datorn (VM) visar att den virtuella datorn har lästs in helt.

2.  Beroende på hur gäst operativ systemet har kon figurer ATS kan det finnas vissa eller ingen nätverks trafik når den virtuella datorn.

## <a name="cause"></a>Orsak

En felaktig konfiguration av gäst systemets brand vägg kan blockera vissa eller alla typer av nätverks trafik till den virtuella datorn.

## <a name="solution"></a>Lösning

Innan du följer dessa steg ska du ta en ögonblicks bild av system disken för den berörda virtuella datorn som en säkerhets kopia. Mer information finns i [ögonblicks bilder av en disk](../windows/snapshot-copy-managed-disk.md).

Du kan felsöka det här problemet genom att använda serie konsolen eller [reparera den virtuella datorn offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline) genom att koppla den virtuella datorns system disk till en virtuell dator för återställning.

## <a name="online-mitigations"></a>Online-begränsningar

Anslut till [serie konsolen och öppna sedan en PowerShell-instans](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Om serie konsolen inte är aktive rad på den virtuella datorn går du till avsnittet "reparera den virtuella datorn offline" i följande Azure-artikel:

 [Ett internt fel inträffar när du försöker ansluta till en virtuell Azure-dator via Fjärrskrivbord](troubleshoot-rdp-internal-error.md#repair-the-vm-offline)

Följande regler kan redige ras för att antingen aktivera åtkomst till den virtuella datorn (via RDP) eller för att ge en enklare fel söknings upplevelse:

*   Fjärr skrivbord (TCP-in): det här är standard regeln som ger primär åtkomst till den virtuella datorn genom att tillåta RDP i Azure.

*   Windows Remote Management (HTTP-in): med den här regeln kan du ansluta till den virtuella datorn med hjälp av PowerShell. i Azure kan du med den här typen av åtkomst använda skript aspekten för fjärrskriptering och fel sökning.

*   Fil-och skrivar delning (SMB-in): den här regeln möjliggör åtkomst till nätverks resurser som fel söknings alternativ.

*   Fil-och skrivar delning (ekobegäran-ICMPv4-in): med den här regeln kan du pinga den virtuella datorn.

I åtkomst instansen för serie konsolen kan du fråga aktuell status för brand Väggs regeln.

*   Fråga med hjälp av visnings namnet som en parameter:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(DisplayName.*<FIREWALL RULE NAME>)" -context 9,4 | more
    ```

*   Fråga genom att använda den lokala port som används i programmet:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<APPLICATION PORT>)" -context 9,4 | more
    ```

*   Fråga med hjälp av den lokala IP-adress som används i programmet:

    ```cmd
    netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalIP.*<CUSTOM IP>)" -context 9,4 | more
    ```

*   Om du ser att regeln är inaktive rad kan du aktivera den genom att köra följande kommando:

    ```cmd
    netsh advfirewall firewall set rule name="<RULE NAME>" new enable=yes
    ```

*   För fel sökning kan du aktivera brand Väggs profilerna:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Om du gör detta för att ange brand väggen korrekt aktiverar du brand väggen igen när du har slutfört fel sökningen.

    > [!Note]
    > Du behöver inte starta om den virtuella datorn för att tillämpa den här ändringen.

*   Försök igen för att ansluta till den virtuella datorn via RDP.

### <a name="offline-mitigations"></a>Offline-åtgärder

1.  Information om hur du aktiverar eller inaktiverar brand Väggs regler finns i [Aktivera eller inaktivera en brand Väggs regel på en Azure VM gäst operativ system](enable-disable-firewall-rule-guest-os.md).

2.  Kontrol lera om du är i [gäst operativ systemets brand vägg som blockerar inkommande trafik scenario](guest-os-firewall-blocking-inbound-traffic.md).

3.  Om du fortfarande är osäker på om brand väggen blockerar åtkomsten kan du läsa [inaktivera gäst operativ systemets brand vägg i Azure VM](disable-guest-os-firewall-windows.md)och sedan återaktivera brand väggen för gäst systemet genom att använda rätt regler.

