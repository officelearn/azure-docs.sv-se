---
title: Felsöka Azure belastningsutjämnare | Microsoft Docs
description: Felsöka kända problem med Azure belastningsutjämnare
services: load-balancer
documentationcenter: na
author: RamanDhillon
manager: timlt
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 69b2caa6cd2fe6d2c89074614aaf28e2ad68a25a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-azure-load-balancer"></a>Felsöka Azure belastningsutjämnare

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Den här sidan innehåller felsökningsinformation för frågor till Azure belastningsutjämnare. När anslutningen belastningsutjämnaren är tillgänglig, är de vanligaste problemen som följer: 
- Virtuella datorer bakom belastningsutjämnaren svarar inte på hälsoavsökningar 
- Virtuella datorer bakom belastningsutjämnaren svarar inte på trafik på den konfigurerade porten

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Symptom: Virtuella datorer bakom belastningsutjämnaren inte svarar på hälsoavsökningar
De måste klara avsökningen kontrollen för backend-servrar att delta i uppsättningen belastningen belastningsutjämnaren. Läs mer om hälsoavsökningar [förstå belastningen belastningsutjämnaren avsökningar](load-balancer-custom-probe-overview.md). 

Belastningsutjämnaren serverdelspoolen VMs svarar inte på avsökningar på grund av något av följande skäl: 
- Belastningsutjämnarens serverdelspool VM är felfri 
- Läsa in serverdelspool VM inte lyssnar på porten för avsökningen 
- Brandvägg eller en nätverkssäkerhetsgrupp blockerar porten på belastningsutjämnaren serverdelspoolen virtuella datorer 
- Andra felaktig konfiguration av belastningsutjämnare

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Orsak 1: Belastningsutjämnarens serverdelspool VM är felfri 

**Validering och lösning**

Lös problemet, logga in på deltagande virtuella datorer, och kontrollera om tillstånd för virtuell dator är felfri och kan svara på **PsPing** eller **TCPing** från en annan virtuell dator i poolen. Om den virtuella datorn är i feltillstånd eller inte svarar på avsökningen, måste du åtgärda problemet och hämta den virtuella datorn till felfritt tillstånd innan den kan delta i belastningsutjämning.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Orsak 2: Belastningsutjämnarens serverdelspool VM inte lyssnar på porten för avsökningen
Om den virtuella datorn är felfri, men svarar inte på avsökningen, en möjlig orsak kan vara att sonden-porten inte är öppen på deltagande VM eller den virtuella datorn inte lyssnar på porten.

**Validering och lösning**

1. Logga in till VM-serverdelen. 
2. Öppna en kommandotolk och kör följande kommando för att validera det är ett program som lyssnar på porten för avsökningen:   
            netstat - ett
3. Om tillståndet porten inte anges som **lyssna**, konfigurera rätt port. 
4. Du kan också välja en annan port som anges som **lyssna**, och därefter ladda konfigurationen av belastningsutjämnaren.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Orsak 3: Brandvägg eller en nätverkssäkerhetsgrupp blockerar port på belastningsutjämnarens serverdelspool virtuella datorer  
Om brandväggen på den virtuella datorn blockerar avsökningsport eller en eller flera nätverk säkerhetsgrupperna som konfigureras i undernät eller på den virtuella datorn, inte tillåter avsökningen att nå porten, är den virtuella datorn inte kan svara på hälsa avsökningen.          

**Validering och lösning**

* Om en brandvägg har aktiverats, kontrollera om den är konfigurerad för att tillåta avsökningsport. Om du inte konfigurera brandväggen att tillåta trafik på avsökningsport och testa igen. 
* Kontrollera om inkommande eller utgående trafik på avsökningsport har störningar i listan över nätverkssäkerhetsgrupper. 
* Kontrollera också om en **neka alla** nätverkssäkerhetsgrupper-regel för nätverkskort på den virtuella datorn eller ett undernät som har högre prioritet än Standardregeln som tillåter LB avsökningar & trafik (nätverkssäkerhetsgrupper måste tillåta Load Balancer IP 168.63.129.16). 
* Om någon av de här reglerna blockerar avsökningen trafik, ta bort och konfigurera regler för att tillåta trafik för avsökning.  
* Testa om den virtuella datorn har nu börjat svarar på hälsa-avsökningar. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Orsak 4: Andra felaktig konfiguration av belastningsutjämnare
Om den föregående orsaker verkar valideras och matcha korrekt och serverdelen VM svarar inte trots att sonden hälsa och sedan manuellt för anslutningstestet och samla in vissa spårningar för att förstå anslutningen.

**Validering och lösning**

* Använd **Psping** från någon av de andra virtuella datorerna inom VNet att testa avsökningen port svar (exempel:.\psping.exe -t 10.0.0.4:3389) och registrerar resultaten. 
* Använd **TCPing** från någon av de andra virtuella datorerna inom VNet att testa avsökningen port svar (exempel:.\tcping.exe 10.0.0.4 3389) och registrerar resultaten. 
* Om inget svar tas emot i dessa tester ping sedan
    - Kör en samtidiga Netsh-spårning på mål-serverdelspoolen VM och en annan Virtuella testdatorn från samma virtuella nätverk. Nu kan köra ett test för PsPing under en viss tid, samla in vissa nätverksspår och stoppa testet. 
    - Analysera nätverket avbildningen och se om det finns både inkommande och utgående paket som är relaterade till ping-frågan. 
        - Om inga inkommande paket observeras på serverdelspoolen VM finns eventuellt en nätverkssäkerhetsgrupper eller UDR felkonfigurerad blockerar trafiken. 
        - Om inga utgående paket observeras på serverdelspoolen VM, måste den virtuella datorn som ska genomsökas efter orelaterade problem (för eample program som blockerar avsökningsport). 
    - Kontrollera om avsökningen paket som tvingas att ett annat mål (eventuellt via UDR inställningar) innan det nådde belastningsutjämnaren. Detta kan orsaka trafiken till aldrig når serverdelens VM. 
* Ändra typen av avsökningen (till exempel HTTP till TCP) och konfigurera motsvarande porten i nätverkssäkerhetsgrupper ACL: er och brandväggen för att kontrollera om problemet beror på konfigurationen av avsökningen svar. Mer information om hälsa avsökningen konfiguration finns [Endpoint belastningsutjämning avsökningen hälsokonfiguration](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Symptom: Virtuella datorer bakom belastningsutjämnaren inte svarar på trafiken på den konfigurerade porten

Om en serverdelspool VM visas som felfri och svarar på hälsoavsökningar, men ändå inte deltar i belastningsutjämning eller svarar inte på data-trafik, kanske den på grund av något av följande skäl: 
* Läsa in Belastningsutjämnarens serverdelspool VM inte lyssnar på dataporten 
* Nätverkssäkerhetsgruppen blockerar porten på belastningsutjämnaren serverdelspoolen VM  
* Åtkomst till belastningsutjämnaren från samma VM och nätverkskort 
* Åtkomst till den Internet VIP för belastningsutjämnare från deltagande belastningsutjämnaren serverdelspoolen VM 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Orsak 1: Belastningsutjämnarens serverdelspool VM inte lyssnar på dataporten 
Om en virtuell dator inte svarar på data-trafik, kan det vara eftersom målporten inte är öppen på deltagande VM eller den virtuella datorn inte lyssnar på porten. 

**Validering och lösning**

1. Logga in till VM-serverdelen. 
2. Öppna en kommandotolk och kör följande kommando för att validera det är ett program som lyssnar på porten för data:  
            netstat - ett 
3. Om porten inte visas med tillståndet ”lyssnar” konfigurera rätt lyssningsport 
4. Om porten är markerad som lyssna, kontrollerar du målprogrammet på porten för möjliga problem. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Orsak 2: Nätverkssäkerhetsgruppen blockerar porten på belastningsutjämnaren serverdelspoolen VM  

Om en eller flera nätverkssäkerhetsgrupper konfigurerad på undernätet eller på den virtuella datorn, blockerar käll-IP och port, och sedan på den virtuella datorn kan inte svara.

* Lista över nätverket säkerhetsgrupperna som konfigureras på serverdelen VM. Mer information finns i [hantera nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md).
* Kontrollera om listan över nätverkssäkerhetsgrupper och:
    - inkommande eller utgående trafik på port data har störningar. 
    - en **neka alla** grupp säkerhetsregeln nätverkskortet för den virtuella datorn eller det undernät som har högre prioritet som Standardregeln som tillåter belastningsutjämnaren avsökningar för nätverk och trafik (nätverkssäkerhetsgrupper måste tillåta belastningen belastningsutjämnaren IP 168.63.129.16 som är avsökningsport) 
* Om någon av reglerna som blockerar trafiken, ta bort och konfigurera om dessa regler för att tillåta datatrafik.  
* Testa om den virtuella datorn har startats nu att svara på hälsa-avsökningar.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Orsak 3: Åtkomst till belastningsutjämnaren från samma gränssnitt för virtuell dator och nätverk 

Om ditt program finns i serverdelens VM av en belastningsutjämnare försöker få åtkomst till ett annat program som finns i samma backend VM via samma nätverksgränssnitt, är ett scenario som stöds inte och kommer att misslyckas. 

**Lösning** du kan lösa det här problemet via någon av följande metoder:
* Konfigurera separata serverdelspool virtuella datorer per program. 
* Konfigurera programmet i virtuella datorer med dubbla nätverkskort så att varje program med sin egen gränssnitt och IP-adress. 

### <a name="cause-4-accessing-the-internal-load-balancer-vip-from-the-participating-load-balancer-backend-pool-vm"></a>Orsak 4: Åtkomst till den interna VIP för belastningsutjämnare från deltagande belastningsutjämnaren serverdelspoolen VM

Om en ILB VIP har konfigurerats i ett virtuellt nätverk och en av de virtuella datorerna deltagare backend försöker komma åt den interna VIP för belastningsutjämnare, som resulterar i fel. Det här är ett scenario som inte stöds.
**Lösning** utvärdera Programgateway eller andra proxyservrar (till exempel nginx eller haproxy) för att stödja den typen av scenario. Mer information om Application Gateway finns [översikt för Programgateway](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Ytterligare nätverksinsamlingar
Om du vill öppna ett supportärende kan du samla in följande information för en snabbare lösning. Välj en enda serverdel VM att utföra följande test:
- Använd Psping från en serverdel VM: ar inom VNet för att testa avsökningen port svar (exempel: psping 10.0.0.4:3389) och registrerar resultaten. 
- Använda TCPing från en serverdel VM: ar inom VNet för att testa avsökningen port svar (exempel: psping 10.0.0.4:3389) och registrerar resultaten.
- Om inget svar tas emot i dessa tester ping kör du en samtidig Netsh-spårning på serverdelen Virtuella och den Virtuella testdatorn i virtuella nätverk när du kör PsPing och sedan stoppa Netsh-spårning. 
  
## <a name="next-steps"></a>Nästa steg

Om de föregående stegen inte löser problemet kan du öppna en [stöder biljett](https://azure.microsoft.com/support/options/).

