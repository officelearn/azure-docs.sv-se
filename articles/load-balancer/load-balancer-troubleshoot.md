---
title: Felsök Azure Load Balancer | Microsoft Docs
description: Felsöka kända problem med Azure Load Balancer
services: load-balancer
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 1a4be7b5caba751f0f90e865d8ef23e5e9c899d6
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2018
ms.locfileid: "42058467"
---
# <a name="troubleshoot-azure-load-balancer"></a>Felsök Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Den här sidan innehåller felsökningsinformation för vanliga frågor för Azure Load Balancer. När anslutningen belastningsutjämnare är tillgängligt kan är de vanligaste symptomen följande: 
- Virtuella datorer bakom belastningsutjämnaren svarar inte på hälsoavsökningar 
- Virtuella datorer bakom belastningsutjämnaren svarar inte på trafik på den konfigurerade porten

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Symptom: Virtuella datorer bakom belastningsutjämnaren svarar inte på hälsoavsökningar
De måste klara avsökningskontroll för backend-servrarna att delta i belastningsutjämningsuppsättningen. Läs mer om hälsoavsökningar [förstå Load Balancer kontrollerar](load-balancer-custom-probe-overview.md). 

Serverdelspool för belastningsutjämnaren virtuella datorer kanske inte svarar på avsökningar på grund av något av följande skäl: 
- Belastningsutjämnarens serverdelspool virtuell dator är i feltillstånd 
- Belastningsutjämnarens serverdelspool VM inte lyssnar på avsökningsporten 
- Brandvägg eller en nätverkssäkerhetsgrupp blockerar port på belastningsutjämnaren serverdelspoolen virtuella datorer 
- Andra inställningar i belastningsutjämnaren

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Orsak 1: Serverdelspool för belastningsutjämnaren virtuell dator är i feltillstånd 

**Validering och lösning**

Logga in på deltagande datorer för att lösa problemet och kontrollera om VM-status är felfri och kan svara på **PsPing** eller **TCPing** från en annan virtuell dator i poolen. Om den virtuella datorn är skadad eller kan inte svara på avsökningen, måste du åtgärda problemet och hämta den virtuella datorn till felfritt tillstånd innan den kan kommunicera med Utjämning av nätverksbelastning.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Orsak 2: Serverdelspool för belastningsutjämnaren VM inte lyssnar på avsökningsporten
Om den virtuella datorn är felfri, men svarar inte på avsökningen, en möjlig orsak kan vara att avsökningsporten inte är öppen på deltagande virtuell dator eller den virtuella datorn inte lyssnar på porten.

**Validering och lösning**

1. Logga in på den virtuella datorn på serversidan. 
2. Öppna en kommandotolk och kör följande kommando för att verifiera det är ett program som lyssnar på avsökningsporten:   
            netstat - an
3. Om port tillstånd inte visas som **lyssna**, konfigurera rätt port. 
4. Du kan också välja en annan port, som anges som **lyssna**, och uppdatera därefter läsa in konfigurationen för belastningsutjämnaren.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Orsak 3: Brandvägg eller en nätverkssäkerhetsgrupp blockerar porten på belastningsutjämnarens serverdelspool virtuella datorer  
Om brandväggen på den virtuella datorn blockerar avsökningsporten, eller en eller flera nätverkssäkerhetsgrupper som konfigureras i undernät eller på den virtuella datorn, är inte tillåten om avsökningen för att nå porten, kan den virtuella datorn inte svarar på hälsoavsökningen.          

**Validering och lösning**

* Om en brandvägg har aktiverats, kontrollerar du om den är konfigurerad för att tillåta avsökningsporten. Annars kan du konfigurera brandväggen att tillåta trafik på avsökningsporten och testa igen. 
* Kontrollera om inkommande eller utgående trafik på avsökningsporten har störningar från listan över nätverkssäkerhetsgrupper. 
* Kontrollera också om en **neka alla** nätverkssäkerhetsgrupper-regel för nätverkskortet på den virtuella datorn eller undernätet som har högre prioritet än Standardregeln som tillåter LB avsökningar & trafik (nätverkssäkerhetsgrupper måste tillåta Load Balancer-IP 168.63.129.16). 
* Om någon av de här reglerna blockerar trafiken avsökning, ta bort och konfigurera regler för att tillåta trafik för avsökning.  
* Testa om den virtuella datorn har nu börjat svara på hälsoavsökningarna. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Orsak 4: Andra inställningar i belastningsutjämnaren
Om alla föregående orsaker verkar valideras och matchas korrekt och den virtuella datorn på serversidan fortfarande svarar inte till hälsoavsökningen och sedan manuellt testa anslutningen för och samla in vissa spårningar för att förstå anslutningen.

**Validering och lösning**

* Använd **Psping** från någon av de andra virtuella datorerna i det virtuella nätverket kan testa avsökningen port svar (exempel:.\psping.exe -t 10.0.0.4:3389) och registrera resultaten. 
* Använd **TCPing** från någon av de andra virtuella datorerna i det virtuella nätverket kan testa avsökningen port svar (exempel:.\tcping.exe 10.0.0.4 3389) och registrera resultaten. 
* Om inget svar tas emot i dessa Pingtest sedan
    - Kör en samtidig Netsh-spårning på serverdelspoolen mål VM och en annan virtuell test VM från samma virtuella nätverk. Nu kan köra ett test för PsPing under en viss tid, samla in vissa nätverksspår och stoppa sedan testet. 
    - Analysera nätverksbild och se om det finns både inkommande och utgående paket som är relaterade till ping-frågan. 
        - Om inga inkommande paket observeras i serverdelspoolen VM, finns det potentiellt en nätverkssäkerhetsgrupper eller UDR felkonfigurerad som blockerar trafiken. 
        - Om ingen utgående paket observeras i serverdelspoolen VM, måste den virtuella datorn som ska genomsökas efter orelaterade problem (för lagringsdiskar; program som blockerar avsökningsporten). 
    - Kontrollera om avsökningen paketen som tvingas att ett annat mål (eventuellt via UDR inställningar) innan de når belastningsutjämnaren. Detta kan orsaka trafiken till når aldrig den virtuella datorn på serversidan. 
* Ändringstyp avsökning (till exempel HTTP till TCP) och konfigurera den motsvarande porten i nätverkssäkerhetsgrupper ACL: er och brandväggen för att verifiera om problemet beror på konfigurationen av avsökningen svar. Läs mer om hälsotillstånd avsökningskonfiguration [Endpoint Load Balancing hälsotillstånd avsökningskonfiguration](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Symptom: Virtuella datorer bakom belastningsutjämnaren svarar inte på trafik på dataporten konfigurerade

Om en serverdelspool VM som felfri och svarar på hälsoavsökningar, men fortfarande inte deltar i Utjämning av nätverksbelastning eller svarar inte på data-trafik, kanske den på grund av något av följande skäl: 
* Belastningsutjämnarens serverdelspool VM inte lyssnar på dataporten 
* Nätverkssäkerhetsgrupp blockerar port på belastningsutjämnaren serverdelspoolen VM  
* Åtkomst till belastningsutjämnaren från samma VM och NIC 
* Åtkomst till Internet-belastningsutjämnare klientdelen från deltagande belastningsutjämnaren serverdelspoolen VM 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Orsak 1: Serverdelspool för belastningsutjämnaren VM inte lyssnar på dataporten 
Om en virtuell dator inte svarar på data-trafik, det kan vara eftersom målporten inte är öppen på den deltagande virtuell datorn eller den virtuella datorn inte lyssnar på porten. 

**Validering och lösning**

1. Logga in på den virtuella datorn på serversidan. 
2. Öppna en kommandotolk och kör följande kommando för att verifiera det är ett program som lyssnar på dataporten:  
            netstat - an 
3. Om porten inte visas med tillståndet ”lyssnar” konfigurera rätt lyssningsport 
4. Om porten har markerats som lyssna, kontrollerar du målprogrammet på porten för alla eventuella problem. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Orsak 2: Nätverkssäkerhetsgrupp blockerar port på belastningsutjämnaren serverdelspoolen VM  

Om en eller flera nätverkssäkerhetsgrupper konfigurerad på undernätet eller på den virtuella datorn, blockerar käll-IP eller port, och sedan på Virtuellt datorn inte svarar.

* Lista över nätverkssäkerhetsgrupper som konfigureras på den virtuella datorn på serversidan. Mer information finns i [hantera nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md).
* Från listan över nätverkssäkerhetsgrupper, kontrollerar du om:
    - inkommande eller utgående trafik på dataporten med störningar. 
    - en **neka alla** nätverks-regel för nätverkssäkerhetsgrupper på nätverkskortet på den virtuella datorn eller undernätet som har högre prioritet som standardregel som tillåter belastningsutjämnaren avsökningar och trafik (nätverkssäkerhetsgrupper måste tillåta Load Balancer IP-Adressen för 168.63.129.16, Det är avsökningsporten) 
* Om någon av reglerna som blockerar trafiken, ta bort och konfigurera om dessa regler för att tillåta datatrafik.  
* Testa om den virtuella datorn nu har börjat svara på hälsoavsökningarna.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Orsak 3: Åtkomst till belastningsutjämnaren från samma gränssnitt för virtuella datorer och nätverk 

Om ditt program på den virtuella datorn av en belastningsutjämnare på serversidan försöker få åtkomst till ett annat program på samma serverdel VM över samma nätverksgränssnitt, är inte och kommer att misslyckas. 

**Lösning** du kan lösa det här problemet via någon av följande metoder:
* Konfigurera separata serverdelspoolen virtuella datorer per program. 
* Konfigurera programmet i virtuella datorer med dubbla nätverkskort så att varje program med sin egen nätverksgränssnitt och IP-adress. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Orsak 4: Åtkomst till interna belastningsutjämnare klientdelen från deltagande belastningsutjämnaren serverdelspoolen VM

Om en intern belastningsutjämnare konfigureras i ett virtuellt nätverk och en av de virtuella datorerna som deltagare serverdel försöker komma åt den interna belastningsutjämnaren klientdelen, kan fel inträffa när flödet är mappad till den ursprungliga virtuella datorn. Det här scenariot stöds inte. Granska [begränsningar](load-balancer-overview.md#limitations) detaljerad information.

**Lösning** det finns flera sätt att avblockera det här scenariot, inklusive en proxy. Utvärdera Application Gateway eller andra 3 part proxyservrar (till exempel nginx eller haproxy). Läs mer om Application Gateway, [översikt över Application Gateway](../application-gateway/application-gateway-introduction.md)

## <a name="additional-network-captures"></a>Ytterligare nätverksinsamlingar
Samla in följande information för en snabbare lösning om du vill öppna ett supportärende. Välj en enda serverdel virtuell dator för att utföra följande test:
- Använd Psping från någon av serverdelen virtuella datorer i det virtuella nätverket för att testa avsökningen port svar (exempel: psping 10.0.0.4:3389) och registrera resultaten. 
- Om inget svar tas emot i dessa Pingtest, kör du en samtidig Netsh-spårning på den virtuella datorn på serversidan och den Virtuella testdatorn i VNet när du kör PsPing och sedan stoppa Netsh-spårning. 
  
## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet kan du öppna en [supportärende](https://azure.microsoft.com/support/options/).

