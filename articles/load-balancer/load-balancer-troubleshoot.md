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
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: a1a8df6d503ec5f5bf9c1e739e5ecf6486a85776
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94697428"
---
# <a name="troubleshoot-azure-load-balancer"></a>Felsök Azure Load Balancer

Den här sidan innehåller felsöknings information för grundläggande och vanliga Azure Load Balancer frågor. Mer information om Standard Load Balancer finns i [standard Load Balancer översikt](load-balancer-standard-diagnostics.md).

När Load Balancer anslutningen inte är tillgänglig är de vanligaste symptomen följande: 

- Virtuella datorer bakom Load Balancer svarar inte på hälso avsökningar 
- Virtuella datorer bakom Load Balancer svarar inte på trafiken på den konfigurerade porten

När de externa klienterna till de virtuella server dels datorerna går via belastningsutjämnaren, används IP-adressen för klienterna för kommunikationen. Kontrol lera att IP-adressen för klienterna läggs till i listan över tillåtna NSG. 

## <a name="symptom-no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>Symptom: ingen utgående anslutning från standard-interna belastningsutjämnare (ILB)

**Verifiering och lösning**

Standard-ILB är **säkra som standard**. Basic-ILB tillåts ansluta till Internet via en *dold* offentlig IP-adress. Detta är inte rekommenderade för produktions arbets belastningar eftersom IP-adressen varken är statisk eller låst via NSG: er som du äger. Om du nyligen har flyttat från en grundläggande ILB till en standard-ILB bör du skapa en offentlig IP-adress direkt via en [utgående](egress-only.md) konfiguration som låser IP-adressen via NSG: er. Du kan också använda en [NAT-gateway](../virtual-network/nat-overview.md) på ditt undernät.

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Symptom: virtuella datorer bakom Load Balancer svarar inte på hälso avsökningar
För att backend-servrarna ska delta i belastnings Utjämnings uppsättningen måste de klara avsöknings kontrollen. Mer information om hälso avsökningar finns i [förstå Load Balancer avsökningar](load-balancer-custom-probe-overview.md). 

Load Balancer de virtuella datorerna i fjärrpoolen kanske inte svarar på avsökningarna på grund av någon av följande orsaker: 
- Load Balancer backend-poolens VM-pool är inte felfri 
- Load Balancer VM-adresspoolen lyssnar inte på avsöknings porten 
- Brand väggen eller en nätverks säkerhets grupp blockerar porten på de virtuella datorerna i Load Balancer backend-poolen 
- Andra felkonfigurationer i Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Orsak 1: den virtuella datorn för Load Balancer backend-poolen är inte felfri 

**Verifiering och lösning**

Lös problemet genom att logga in på de deltagande virtuella datorerna och kontrol lera om tillståndet för den virtuella datorn är felfritt och kan svara på **PsPing** eller **TCPing** från en annan virtuell dator i poolen. Om den virtuella datorn är ohälsosam eller inte kan svara på avsökningen, måste du åtgärda problemet och återställa den till ett felfritt tillstånd innan den kan ingå i belastnings utjämningen.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Orsak 2: den virtuella datorn i Load Balancer backend-poolen lyssnar inte på avsöknings porten
Om den virtuella datorn är felfri, men inte svarar på avsökningen, kan en möjlig orsak vara att avsöknings porten inte är öppen på den deltagande virtuella datorn eller att den virtuella datorn inte lyssnar på den porten.

**Verifiering och lösning**

1. Logga in på den virtuella datorns Server del. 
2. Öppna en kommando tolk och kör följande kommando för att verifiera att det finns ett program som lyssnar på avsöknings porten:   
            Netstat-a
3. Om Port statusen inte visas som **avlyssning** konfigurerar du rätt port. 
4. Du kan också välja en annan port, som visas som **lyssning** och uppdatera belastnings Utjämnings konfigurationen.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Orsak 3: brand vägg eller en nätverks säkerhets grupp blockerar porten på de virtuella datorerna för belastningsutjämnare i backend-poolen  
Om brand väggen på den virtuella datorn blockerar avsöknings porten, eller om en eller flera nätverks säkerhets grupper som kon figurer ATS i under nätet eller på den virtuella datorn, inte tillåter avsökningen att kontakta porten, kan den virtuella datorn inte svara på hälso avsökningen.          

**Verifiering och lösning**

* Om brand väggen är aktive rad kontrollerar du om den är konfigurerad för att tillåta avsöknings porten. Om inte, konfigurerar du brand väggen så att den tillåter trafik på avsöknings porten och testar igen. 
* I listan över nätverks säkerhets grupper kontrollerar du om inkommande eller utgående trafik på avsöknings porten har störningar. 
* Kontrol lera också om regeln **Neka alla** nätverks säkerhets grupper på nätverkskortet för den virtuella datorn eller under nätet som har högre prioritet än standard regeln som tillåter lb-avsökning & trafik (nätverks säkerhets grupper måste tillåta Load Balancer IP för 168.63.129.16). 
* Om någon av dessa regler blockerar avsöknings trafiken tar du bort och konfigurerar om reglerna för att tillåta avsöknings trafiken.  
* Testa om den virtuella datorn nu har börjat svara på hälso avsökningarna. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Orsak 4: andra fel konfigurationer i Load Balancer
Om alla föregående orsaker verkar vara verifierade och lösta korrekt, och backend-datorn fortfarande inte svarar på hälso avsökningen, kan du manuellt testa anslutningen och samla in vissa spår för att förstå anslutningen.

**Verifiering och lösning**

* Använd **Psping** från någon av de andra virtuella datorerna i det virtuella nätverket för att testa avsöknings portens svar (exempel: .\psping.exe-t 10.0.0.4:3389) och registrera resultat. 
* Använd **TCPing** från någon av de andra virtuella datorerna i det virtuella nätverket för att testa avsöknings portens svar (exempel: .\tcping.exe 10.0.0.4 3389) och registrera resultat. 
* Om inget svar tas emot i dessa ping-test,
    - Kör en samtidig netsh-spårning på mål server delens virtuella dator och en annan virtuell test dator från samma VNet. Kör nu ett PsPing-test under en viss tid, samla in några nätverks spår och stoppa sedan testet. 
    - Analysera nätverks avbildningen och se om det finns både inkommande och utgående paket relaterade till ping-frågan. 
        - Om inga inkommande paket observerats på den virtuella backend-poolen, finns det potentiellt nätverks säkerhets grupper eller UDR-konfiguration som blockerar trafiken. 
        - Om inga utgående paket observeras på den virtuella backend-poolen måste den virtuella datorn kontrol leras för eventuella orelaterade problem (till exempel program som blockerar avsöknings porten). 
    - Kontrol lera att avsöknings paketen tvingas till ett annat mål (eventuellt via UDR-inställningar) innan belastningsutjämnaren når belastningsutjämnaren. Detta kan orsaka att trafiken aldrig når den virtuella backend-datorn. 
* Ändra avsöknings typ (till exempel HTTP till TCP) och konfigurera motsvarande port i nätverks säkerhets grupper ACL: er och brand vägg för att kontrol lera om problemet beror på konfigurationen av avsöknings svaret. Mer information om konfiguration av hälso avsökning finns i [konfiguration av hälso avsöknings konfiguration för slut punkts belastning](/archive/blogs/mast/endpoint-load-balancing-heath-probe-configuration-details).

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Symptom: virtuella datorer bakom Load Balancer svarar inte på trafik på den konfigurerade data porten

Om en virtuell dator i en virtuell dator i listan visas som felfri och svarar på hälso avsökningarna, men fortfarande inte ingår i belastnings utjämningen eller inte svarar på data trafiken, kan det bero på någon av följande orsaker: 
* Load Balancer VM-adresspoolen lyssnar inte på data porten 
* Nätverks säkerhets gruppen blockerar porten på den virtuella datorn i Load Balancer backend-poolen  
* Åtkomst till Load Balancer från samma virtuella dator och NIC 
* Åtkomst till Internet Load Balancer-frontend från den deltagande Load Balancer backend-poolen VM 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Orsak 1: den virtuella datorn i Load Balancer backend-poolen lyssnar inte på data porten 
Om en virtuell dator inte svarar på data trafiken kan det bero på att mål porten inte är öppen på den deltagande virtuella datorn eller att den virtuella datorn inte lyssnar på den porten. 

**Verifiering och lösning**

1. Logga in på den virtuella datorns Server del. 
2. Öppna en kommando tolk och kör följande kommando för att verifiera att det finns ett program som lyssnar på data porten:  
            Netstat-a 
3. Om porten inte finns med i status "lyssning" konfigurerar du rätt lyssnar port 
4. Om porten är markerad som avlyssning kontrollerar du mål programmet på den porten för eventuella problem.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Orsak 2: nätverks säkerhets gruppen blockerar porten på den virtuella datorn Load Balancer backend-poolen  

Om en eller flera nätverks säkerhets grupper som kon figurer ATS i under nätet eller på den virtuella datorn blockerar käll-IP eller port, kommer den virtuella datorn inte att svara.

För den offentliga belastningsutjämnaren kommer IP-adressen för Internet-klienter att användas för kommunikation mellan klienterna och de virtuella datorerna för belastningsutjämnare. Kontrol lera att klientens IP-adress är tillåtna i den virtuella datorns nätverks säkerhets grupp.

1. Lista de nätverks säkerhets grupper som kon figurer ATS på den virtuella datorn. Mer information finns i [Hantera nätverks säkerhets grupper](../virtual-network/manage-network-security-group.md)
1. I listan över nätverks säkerhets grupper kontrollerar du om:
    - inkommande eller utgående trafik på data porten har störningar. 
    - en regel för att **Neka alla** nätverks säkerhets grupper på nätverkskortet för den virtuella datorn eller under nätet som har en högre prioritet som standard regeln som tillåter Load Balancer avsökningar och trafik (nätverks säkerhets grupper måste tillåta Load Balancer IP-adress för 168.63.129.16, som är avsöknings port)
1. Om någon av reglerna blockerar trafiken tar du bort och konfigurerar om reglerna för att tillåta data trafiken.  
1. Testa om den virtuella datorn nu har börjat svara på hälso avsökningarna.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Orsak 3: komma åt Load Balancer från samma virtuella dator och nätverks gränssnitt 

Om programmet som finns på den virtuella server delen av en Load Balancer försöker få åtkomst till ett annat program som finns i samma server dels dator i samma nätverks gränssnitt, så är det ett scenario som inte stöds och inte fungerar. 

**Lösning** Du kan lösa det här problemet via någon av följande metoder:
* Konfigurera separata VM-pooler per program. 
* Konfigurera programmet i virtuella datorer med dubbla nätverkskort så att varje program använder sitt eget nätverks gränssnitt och IP-adress. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Orsak 4: komma åt den interna Load Balancer-frontend-filen från den deltagande Load Balancer backend-poolen VM

Om ett internt Load Balancer har kon figurer ATS i ett virtuellt nätverk och en av de virtuella datorerna för en deltagar Server försöker komma åt den interna Load Balancer-frontend, kan fel uppstå när flödet mappas till den ursprungliga virtuella datorn. Det här scenariot stöds inte.

**Lösning** Det finns flera sätt att avblockera det här scenariot, inklusive att använda en proxy. Utvärdera Application Gateway eller andra tredjeparts-proxyservrar (till exempel nginx eller haproxy). Mer information om Application Gateway finns i [Översikt över Application Gateway](../application-gateway/overview.md)

**Information** Interna belastningsutjämnare översätter inte utgående, ursprungliga anslutningar till klient delen av en intern Load Balancer eftersom båda finns i privata IP-adressutrymme. Publika belastningsutjämnare tillhandahåller [utgående anslutningar](load-balancer-outbound-connections.md) från privata IP-adresser i det virtuella nätverket till offentliga IP-adresser. För interna belastningsutjämnare gör den här metoden att du undviker möjliga SNAT-portar i ett unikt internt IP-adressutrymme, där översättning inte krävs.

En sido effekt är att om ett utgående flöde från en virtuell dator i backend-poolen försöker använda ett flöde till klient delen av den interna Load Balancer i poolen _och_ som mappas tillbaka till sig själv, matchar inte de två benen i flödet. Eftersom de inte matchar, Miss lyckas flödet. Flödet lyckas om flödet inte mappades tillbaka till samma virtuella dator i backend-poolen som skapade flödet till klient delen.

När flödet mappar tillbaka till sig själv, visas det utgående flödet som härstammar från den virtuella datorn till klient delen och motsvarande inkommande flöde visas som härstammar från den virtuella datorn till sig själv. Från gäst operativ systemets visnings punkt matchar de inkommande och utgående delarna av samma flöde inte i den virtuella datorn. TCP-stacken känner inte igen de hälften av samma flöde som en del av samma flöde. Källan och målet matchar inte. När flödet mappar till en annan virtuell dator i backend-poolen matchar hälften av flödet och den virtuella datorn kan svara på flödet.

Symptomet för det här scenariot är tillfälligt anslutnings-timeout när flödet återgår till samma server del som initierade flödet. Vanliga lösningar är infogning av ett proxy-lager bakom den interna Load Balancer och användnings regler för direkt Server retur (DSR). Mer information finns i [flera klient delar för Azure Load Balancer](load-balancer-multivip-overview.md).

Du kan kombinera ett internt Load Balancer med valfri tredjeparts-proxy eller använda interna [Application Gateway](../application-gateway/overview.md) för proxy-scenarier med http/https. Även om du kan använda en offentlig Load Balancer för att undvika det här problemet, är det här scenariot känsligt för [SNAT-belastning](load-balancer-outbound-connections.md). Undvik den andra metoden om du inte noggrant hanterar den.

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>Symptom: det går inte att ändra backend-porten för en befintlig LB-regel för en belastningsutjämnare vars VM Scale Set har distribuerats i backend-poolen. 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>Orsak: Server dels porten kan inte ändras för en belastnings Utjämnings regel som används av en hälso avsökning för belastningsutjämnaren som refereras till av en skalnings uppsättning för virtuella datorer.
**Lösning** För att kunna ändra porten kan du ta bort hälso avsökningen genom att uppdatera den virtuella datorns skalnings uppsättning, uppdatera porten och sedan konfigurera hälso avsökningen igen.

## <a name="symptom-small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>Symptom: liten trafik går fortfarande igenom belastningsutjämnaren när de virtuella datorerna har tagits bort från belastningsutjämnaren. 
### <a name="cause--vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Orsak: virtuella datorer som har tagits bort från backend-poolen bör inte längre ta emot trafik. Den lilla mängden nätverks trafik kan vara relaterad till lagring, DNS och andra funktioner i Azure. 
För att verifiera kan du utföra en nätverks spårning. FQDN som används för Blob Storage-konton visas i egenskaperna för varje lagrings konto.  Från en virtuell dator i din Azure-prenumeration kan du utföra en nslookup för att avgöra vilken Azure-IP-adress som tilldelats det lagrings kontot.

## <a name="additional-network-captures"></a>Ytterligare nätverks avbildningar
Om du väljer att öppna ett support ärende samlar du in följande information för en snabbare lösning. Välj en enskild virtuell dator för att utföra följande tester:
- Använd Psping från en av de virtuella datorerna i det virtuella nätverket för att testa avsöknings portens svar (exempel: Psping 10.0.0.4:3389) och registrera resultat. 
- Om inget svar tas emot i de här ping-testerna kör du en samtidig netsh-spårning på den virtuella datorns VM och VNet-testets VM medan du kör PsPing och stoppar sedan netsh-spårningen. 
 
## <a name="symptom-load-balancer-in-failed-state"></a>Symptom: Load Balancer i felaktigt tillstånd 

**Lösning**

- När du har identifierat den resurs som är i fel tillstånd går du till [Azure Resource Explorer](https://resources.azure.com/) och identifierar resursen i det här läget. 
- Uppdatera växlingen till höger övre övre hörn för att läsa/skriva.
- Klicka på Redigera för resursen i felaktigt tillstånd.
- Klicka på Lägg till, följt av GET för att se till att etablerings statusen har uppdaterats till slutfört.
- Sedan kan du fortsätta med andra åtgärder eftersom resursen inte är i felläge.


## <a name="next-steps"></a>Nästa steg

Om föregående steg inte löser problemet öppnar du ett [support ärende](https://azure.microsoft.com/support/options/).