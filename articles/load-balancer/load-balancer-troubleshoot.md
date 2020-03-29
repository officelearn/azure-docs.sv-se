---
title: Felsök Azure Load Balancer
description: Lär dig hur du felsöker kända problem med Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: ca9b70bd71a618f8e3d5f4fe9504ba66a9f14c6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935484"
---
# <a name="troubleshoot-azure-load-balancer"></a>Felsök Azure Load Balancer

Den här sidan innehåller felsökningsinformation för vanliga Azure Load Balancer-frågor för grundläggande och standard. Mer information om standardbelastningsutjämnare finns i [översikt över standardbelastningsutjämning](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics).

När anslutningsanslutningen för belastningsutjämnaranslutningen inte är tillgänglig är de vanligaste symptomen följande: 

- Virtuella datorer bakom belastningsutjämnaren svarar inte på hälsoavsökningar 
- Virtuella datorer bakom belastningsutjämnren svarar inte på trafiken på den konfigurerade porten

När de externa klienterna till de virtuella datorerna för backend går igenom belastningsutjämnaren används KLIENTERNAS IP-adress för kommunikationen. Kontrollera att KLIENTERNAS IP-adress läggs till i listan tillåt nsg.Make sure the IP address of the clients are added in the NSG allow list. 

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Symptom: Virtuella datorer bakom belastningsutjämnaren svarar inte på hälsoavsökningar
För att serverdservrarna ska kunna delta i belastningsutjämnadsuppsättningen måste de klara avsökningskontrollen. Mer information om hälsoavsökningar finns i [Förstå belastningsutjämnare avsökningar](load-balancer-custom-probe-overview.md). 

De virtuella datorerna för serverda i belastningsutjämnarpoolen kanske inte svarar på avsökningarna på grund av något av följande skäl: 
- Load Balancer backend pool VM är felfritt 
- Load Balancer backend pool VM lyssnar inte på sondporten 
- Brandvägg eller en nätverkssäkerhetsgrupp blockerar porten på de virtuella datorerna för belastningsutjämnares backend-pool 
- Andra felkonfigurationer i belastningsutjämnare

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Orsak 1: Vm-serverda i serverdapoolen för belastningsutjämning är felaktig 

**Validering och upplösning**

LÃ¶s problemet genom att logga in pÃ¥ de deltagande virtuella datorerna och kontrollera om tillståndet fÃ¶r virtuella datorer är felfritt och kan svara **pÃ¥ PsPing** eller **TCPing** frÃ¥r en annan virtuell dator i poolen. Om den virtuella datorn är felfritt eller inte kan svara på avsökningen, måste du åtgärda problemet och få den virtuella datorn tillbaka till ett felfritt tillstånd innan den kan delta i belastningsutjämning.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Orsak 2: Vm-serverda i belastningsutjämnarens serverd-pool lyssnar inte på avsökningsporten
Om den virtuella datorn är felfri, men inte svarar på avsökningen, kan en möjlig orsak vara att avsökningsporten inte är öppen på den deltagande virtuella datorn eller att den virtuella datorn inte lyssnar på den porten.

**Validering och upplösning**

1. Logga in på den virtuella datorn för fjärrsnämjning. 
2. Öppna en kommandotolk och kör följande kommando för att verifiera att det finns ett program som lyssnar på avsökningsporten:   
            netstat -en
3. Om porttillståndet inte visas som **LYSSNA**konfigurerar du rätt port. 
4. Du kan också välja en annan port som visas som **LYSSNA och**uppdatera belastningsutjämnarens konfiguration därefter.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Orsak 3: Brandvägg eller en nätverkssäkerhetsgrupp blockerar porten på de virtuella datorerna för belastningsutjämnares backend-pool  
Om brandväggen på den virtuella datorn blockerar avsökningsporten, eller en eller flera nätverkssäkerhetsgrupper som konfigurerats på undernätet eller på den virtuella datorn, inte tillåter avsökningen att nå porten, kan den virtuella datorn inte svara på hälsoavsökningen.          

**Validering och upplösning**

* Om brandväggen är aktiverad kontrollerar du om den är konfigurerad för att tillåta avsökningsporten. Om inte, konfigurera brandväggen så att den tillåter trafik på avsökningsporten och testa igen. 
* Kontrollera om inkommande eller utgående trafik på avsökningsporten har störningar i listan över nätverkssäkerhetsgrupper. 
* Kontrollera också om regeln **Neka alla** nätverkssäkerhetsgrupper på nätverkskortet för den virtuella datorn eller undernätet som har högre prioritet än standardregeln som tillåter LB-avsökningar & trafik (nätverkssäkerhetsgrupper måste tillåta belastningsutjämnad IP på 168.63.129.16). 
* Om någon av dessa regler blockerar avsökningstrafiken tar du bort och konfigurerar om reglerna så att avsökningstrafiken tillåts.  
* Testa om den virtuella datorn nu har börjat svara på hälsoavsökningarna. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Orsak 4: Andra felkonfigurationer i belastningsutjämnaren
Om alla föregående orsaker verkar vara validerade och lösta korrekt, och den virtuella datorn för serverda fortfarande inte svarar på hälsoavsökningen, testas sedan manuellt för anslutning och samlar in vissa spårningar för att förstå anslutningen.

**Validering och upplösning**

* Använd **Psping** från en av de andra virtuella datorerna i det virtuella nätverket för att testa avsökningsportens svar (exempel: .\psping.exe -t 10.0.0.4:3389) och spela in resultat. 
* Använd **TCPing** från en av de andra virtuella datorerna i det virtuella nätverket för att testa avsökningsportens svar (exempel: .\tcping.exe 10.0.0.4 3389) och spela in resultat. 
* Om inget svar tas emot i dessa ping-tester,
    - Kör en samtidig Netsh-spårning på målbackspoolen VM och en annan test-VM från samma virtuella nätverk. Nu, kör en PsPing test under en tid, samla några nätverk spår, och sedan stoppa testet. 
    - Analysera nätverksinfångningen och se om det finns både inkommande och utgående paket relaterade till ping-frågan. 
        - Om inga inkommande paket observeras på den virtuella datorn för serverdapoolen finns det potentiellt en nätverkssäkerhetsgrupper eller UDR-felkonfiguration som blockerar trafiken. 
        - Om inga utgående paket observeras på den virtuella datorn för serverdapoolen måste den virtuella datorn kontrolleras för eventuella orelaterade problem (till exempel Program som blockerar avsökningsporten). 
    - Kontrollera om avsökningspaketen tvingas till ett annat mål (eventuellt via UDR-inställningar) innan belastningsutjämnaren nårs. Detta kan leda till att trafiken aldrig når den virtuella datorn för säkerhetskopiering. 
* Ändra avsökningstypen (till exempel HTTP till TCP) och konfigurera motsvarande port i nätverksskyddsgruppers ACL:er och brandväggen för att validera om problemet är med konfigurationen av avsökningssvar. Mer information om hälsoavsökningskonfiguration finns i [Hälsoavsökningskonfiguration för slutpunktsbelastningsutjämning](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Symptom: Virtuella datorer bakom belastningsutjämnare svarar inte på trafiken i den konfigurerade dataporten

Om en vm-serverdelspool visas som felfri och svarar på hälsoavsökningarna, men fortfarande inte deltar i belastningsutjämningen eller inte svarar på datatrafiken, kan det bero på något av följande: 
* Load Balancer Backend pool VM lyssnar inte på dataporten 
* Nätverkssäkerhetsgruppen blockerar porten på den virtuella datorn för belastningsutjämningshanterarens bakåtsträvande pool  
* Komma åt belastningsutjämnaren från samma virtuella dator och nätverkskort 
* Åtkomst till frontend för Internet Load Balancer från den deltagande vm-poolen för belastningsutjämnare i backend 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Orsak 1: Vm-poolen för belastningsutjämnare-backend-poolen lyssnar inte på dataporten 
Om en virtuell dator inte svarar på datatrafiken kan det bero på att antingen målporten inte är öppen på den deltagande virtuella datorn eller att den virtuella datorn inte lyssnar på den porten. 

**Validering och upplösning**

1. Logga in på den virtuella datorn för fjärrsnämjning. 
2. Öppna en kommandotolk och kör följande kommando för att verifiera  att det finns ett program som lyssnar på dataporten: netstat -an 
3. Om porten inte visas med tillstånd "LYSSNA" konfigurerar du rätt lyssnarport 
4. Om porten är markerad som Lyssna kontrollerar du målprogrammet på den porten för eventuella problem.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Orsak 2: Nätverkssäkerhetsgruppen blockerar porten på den virtuella datorn för belastningsutjämnadsbackend  

Om en eller flera nätverkssäkerhetsgrupper som konfigurerats i undernätet eller på den virtuella datorn blockerar käll-IP-adressen eller porten, kan den virtuella datorn inte svara.

För den offentliga belastningsutjämnaren används IP-adressen för Internet-klienterna för kommunikation mellan klienterna och de virtuella datorerna för belastningsutjämnare. Kontrollera att IP-adressen för klienterna är tillåtna i den virtuella datorn för säkerhetskopiering.

1. Lista de nätverkssäkerhetsgrupper som konfigurerats på den virtuella datorn för serverda. Mer information finns i [Hantera nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md)
1. Kontrollera om:
    - inkommande eller utgående trafik på dataporten har störningar. 
    - en **regel för neka alla** nätverksskyddsgrupper på nätverkskortet för den virtuella datorn eller undernätet som har högre prioritet som standardregeln som tillåter belastningsutjämnare och trafik (nätverkssäkerhetsgrupper måste tillåta belastningsutjämnad IP på 168.63.129.16, det vill säga avsökningsport)
1. Om någon av reglerna blockerar trafiken tar du bort och konfigurerar om dessa regler så att datatrafiken tillåts.  
1. Testa om den virtuella datorn nu har börjat svara på hälsoavsökningarna.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Orsak 3: Komma åt belastningsutjämnaren från samma virtuella dator och nätverksgränssnitt 

Om programmet finns i den virtuella datorn för fjärrstöd för en belastningsutjämnare försöker komma åt ett annat program som finns i samma start-VM över samma nätverksgränssnitt, är det ett scenario som inte stöds och misslyckas. 

**Upplösning** Du kan lösa problemet via någon av följande metoder:
* Konfigurera separata start-och pool-datorer per program. 
* Konfigurera programmet i dubbla nätverkskort virtuella datorer så att varje program använde sitt eget nätverksgränssnitt och IP-adress. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Orsak 4: Åtkomst till den interna belastningsutjämnarens frontend från den deltagande vm-poolen för belastningsutjämningsstöd

Om en intern belastningsutjämnare har konfigurerats i ett virtuellt nätverk och en av deltagarens serverdels virtuella datorer försöker komma åt den interna belastningsutjämnarfrontend, kan fel uppstå när flödet mappas till den ursprungliga virtuella datorn. Det här scenariot stöds inte. Granska [begränsningar](concepts-limitations.md#limitations) för en detaljerad diskussion.

**Upplösning** Det finns flera sätt att häva blockeringen av det här scenariot, bland annat genom att använda en proxy. Utvärdera Application Gateway eller andra fullmakter från tredje part (till exempel nginx eller haproxy). Mer information om Application Gateway finns i [Översikt över Application Gateway](../application-gateway/application-gateway-introduction.md)

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>Symptom: Det går inte att ändra backend-porten för den befintliga LB-regeln för en belastningsutjämnare som har VM-skalningsuppsättningen distribuerad i backend-poolen. 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>Orsak: Serverdaporten kan inte ändras för en belastningsutjämningsregel som används av en hälsoavsökning för belastningsutjämnare som refereras av VM-skalningsuppsättningen.
**Upplösning** För att ändra porten kan du ta bort hälsoavsökningen genom att uppdatera vm-skaluppsättningen, uppdatera porten och sedan konfigurera hälsoavsökningen igen.

## <a name="additional-network-captures"></a>Ytterligare nätverksinsamlingar
Om du bestämmer dig för att öppna ett supportärende samlar du in följande information för en snabbare lösning. Välj en enda dator med en bakåtsträvning för att utföra följande tester:
- Använd Psping från en av de bakre virtuella datorerna i det virtuella nätverket för att testa avsökningsportens svar (exempel: psping 10.0.0.4:3389) och spela in resultat. 
- Om inget svar tas emot i dessa ping-tester kör du en samtidig Netsh-spårning på den virtuella datorn för säkerhetskopiering och den virtuella datorn för test-VM medan du kör PsPing och stoppa sedan Netsh-spårningen. 
  
## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet öppnar du en [supportbiljett](https://azure.microsoft.com/support/options/).

