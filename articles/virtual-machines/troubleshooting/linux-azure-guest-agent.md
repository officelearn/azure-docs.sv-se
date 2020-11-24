---
title: Felsöka Azure Linux-gäst Gent
description: Felsöka Azure Linux-gästen fungerar inte problem
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 9e3b376cfaf5379acaf92713c42509471200d066
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95550116"
---
# <a name="troubleshooting-azure-linux-guest-agent"></a>Felsöka Azure Linux-gäst Gent

[Azure Linux-gästen agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) är en virtuell dator agent (VM). Den gör det möjligt för den virtuella datorn att kommunicera med infrastruktur styrenheten (den underliggande fysiska server som den virtuella datorn finns på) på IP-168.63.129.16. Den här IP-adressen är en virtuell offentlig IP-adress som underlättar kommunikationen. Mer information finns i [Vad är IP-168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="checking-agent-status-and-version"></a>Kontrollerar agent status och version

Se https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Felsöka VM-agenten som är i status klar

### <a name="step-1-check-whether-the-azure-linux-guest-agent-service-is-running"></a>Steg 1 kontrol lera om Azure Linux gästa Gent tjänsten körs

Beroende på distribution kan tjänst namnet vara walinuxagent eller waagent:

```
root@nam-u18:/home/nam# service walinuxagent status
● walinuxagent.service - Azure Linux Agent
   Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
 Main PID: 1036 (python3)
    Tasks: 4 (limit: 4915)
   CGroup: /system.slice/walinuxagent.service
           ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
           └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers

Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
root@nam-u18:/home/nam#
```


Om du kan se tjänsterna och de körs startar du om tjänsten för att se om problemet är löst. Om tjänsterna stoppas startar du dem och väntar några minuter. Kontrol lera sedan om **agent statusen** rapporteras som **klar**. Kontakta [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om du vill ha ytterligare fel sökning av dessa problem.

### <a name="step-2-check-whether-auto-update-is-enabled"></a>Steg 2 kontrol lera om automatisk uppdatering har Aktiver ATS

Kontrol lera den här inställningen i/etc/waagent.conf:

```
AutoUpdate.Enabled=y
```

Mer information om hur du uppdaterar Azure Linux-agenten finns i https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent 
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Steg 3 kontrol lera om den virtuella datorn kan ansluta till infrastruktur styrenheten

Använd ett verktyg som sväng för att testa om den virtuella datorn kan ansluta till 168.63.129.16 på portarna 80, 32526 och 443. Om den virtuella datorn inte ansluter som förväntat kontrollerar du om utgående kommunikation över portarna 80, 443 och 32526 är öppen i din lokala brand vägg på den virtuella datorn. Om den här IP-adressen är blockerad kan VM-agenten uppvisa oväntat beteende i flera olika scenarier.

## <a name="advanced-troubleshooting"></a>Avancerad felsökning

Händelser för fel sökning av Azure Linux gästa Gent registreras i följande loggfiler:

- /var/log/waagent.log


  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Det går inte att ansluta till WireServer IP (värd-IP) 

Du ser följande fel poster i/var/log/waagent.log:

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

**Analys**

Den virtuella datorn kan inte komma åt WireServer IP på värd servern.

**Lösning**

1. Eftersom IP-WireServer inte kan nås ansluter du till den virtuella datorn med hjälp av SSH och försöker sedan komma åt följande URL från svängen: http://168.63.129.16/?comp=versions 
1. Sök efter eventuella problem som kan ha orsakats av en brand vägg, en proxy eller annan källa som kan blockera åtkomst till IP-168.63.129.16.
1. Kontrol lera om Linux-program varan iptables eller en brand vägg från tredje part blockerar åtkomsten till portarna 80, 443 och 32526. Mer information om varför den här adressen inte ska blockeras finns i [Vad är IP-168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).


## <a name="next-steps"></a>Nästa steg

[Kontakta Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)för att ytterligare felsöka problemet "Windows Azures gästa Gent fungerar inte".
