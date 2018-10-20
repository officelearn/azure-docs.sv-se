---
title: Tid synkronisering för virtuella Linux-datorer i Azure | Microsoft Docs
description: Tidssynkronisering för Linux-datorer.
services: virtual-machines-linux
documentationcenter: ''
author: zr-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: zarhoads
ms.openlocfilehash: aec36b8ea0f342b133e6844e5a96314c6348289b
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49465482"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Tidssynkronisering för virtuella Linux-datorer i Azure

Tidsynkroniseringen är viktigt för säkerhet och händelsekorrelation. Den används ibland för implementering av distribuerade transaktioner. Tid noggrannhet mellan flera datorsystem uppnås via synkronisering. Synkronisering kan påverkas av flera saker, inklusive omstarter och nätverkstrafik mellan tidskällan och den dator som hämtar tiden. 

Azure backas upp av infrastruktur som kör Windows Server 2016. Windows Server 2016 har förbättrade algoritmer som används för att korrekt tid och ange villkor för den lokala klockan att synkronisera med UTC.  Funktionen Windows Server 2016 korrekt tid avsevärt bättre hur VMICTimeSync tjänst som reglerar virtuella datorer med värden för rätt tid. Förbättringarna innefattar mer exakta första gången på VM start eller återställning av virtuella datorer och interrupt svarstid korrigering. 

>[!NOTE]
>Ta en titt på detta för en snabb översikt över Windows tidstjänst [översikt på hög nivå video](https://aka.ms/WS2016TimeVideo).
>
> Mer information finns i [korrekt tid för Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Översikt

Precision för en datorklocka gauged på hur nära datorklockan är tid-standarden Coordinated Universal Time (UTC). UTC definieras av ett multinationella exempel på exakt atomiska åtgärden klockor kan bara vara av av en sekund 300 åren. Men läser UTC direkt kräver specialiserad maskinvara. I stället tidsservrar synkroniseras till UTC och kan nås från andra datorer för att tillhandahålla skalbarhet och stabilitet. Varje dator har tid kör som-synkroniseringstjänsten vet vilken tidsservrar som ska användas och regelbundet kontrollerar om datorklocka måste åtgärdas och justerar tid om det behövs. 

Azure-värdar som synkroniseras till interna Microsoft-tidsservrar att ta sina tid från Microsoft-ägda Stratum 1-enheter med GPS antenner. Virtuella datorer i Azure kan antingen bero på deras värden för att skicka rätt tid (*värd tid*) in på den virtuella datorn eller den virtuella datorn direkt får tid från en time-server eller en kombination av båda. 

På fristående maskinvara, Linux-operativsystem endast läser värdmaskinvaran systemklockan på Start. Efter det underhålls klockan av avbrott timern i Linux-kernel. I den här konfigurationen kommer klockan avviker över tid. I senare Linux-distributioner på Azure kan virtuella datorer använda VMICTimeSync-provider som ingår i Linux integration services (LIS) att fråga efter klockan uppdateringar från värden oftare.

VM-interaktioner med-värden kan också påverka klockan. Under [minne bevarande Underhåll](maintenance-and-updates.md#memory-preserving-maintenance), virtuella datorer har pausats för upp till 30 sekunder. Till exempel innan du påbörjar Underhåll VM klockan visar 10:00:00 AM och varar 28 sekunder. När den virtuella datorn återupptas klockan på den virtuella datorn fortfarande visas 10:00:00 AM, som är 28 sekunder av. Att korrekt för detta, VMICTimeSync tjänsten övervakar vad som händer på värden och frågar efter ändringar på de virtuella datorerna att kompensera.

Klockan på den virtuella datorn skulle ackumuleras fel utan att tid synkronisering fungerar. När det finns bara en virtuell dator, kanske inte effekten är betydande om arbetsbelastningen kräver mycket exakta tidsangivelserna. Men i de flesta fall, vi har flera, sammankopplade virtuella datorer som använder tid för att spåra transaktioner och de tid måste vara konsekvent på hela distributionen. När tiden mellan virtuella datorer skiljer sig, kan du få se följande effekter:

- Misslyckas autentiseringen. Säkerhetsprotokoll som Kerberos eller certifikat-beroende teknik som förlitar sig på tid som konsekvent över systemen.
- Det är mycket svårt att ta reda på vad hände i ett system om loggar (eller andra data) inte godkänner villkoren i tid. Samma händelse skulle se ut som om det händer vid olika tidpunkter, och korrelation svårt.
- Om klockan inte är aktiv kan faktureringen beräknas felaktigt.



## <a name="configuration-options"></a>Konfigurationsalternativ

Det finns vanligtvis tre sätt att konfigurera tidssynkronisering för din virtuella Linux-datorer i Azure:

- Standardkonfigurationen för Azure Marketplace-avbildningar använder både NTP-tid och VMICTimeSync-tiden på värddatorn. 
- Värden endast med hjälp av VMICTimeSync.
- Använd en annan, externa tidsserver med eller utan att använda VMICTimeSync-tiden på värddatorn.


### <a name="use-the-default"></a>Använd standard

Som standard är de flesta Azure Marketplace-avbildningar för Linux är konfigurerad att synkronisera från två källor: 

- NTP som primär som hämtar tid från en NTP-server. Till exempel Ubuntu 16.04 LTS Marketplace-avbildningar används **ntp.ubuntu.com**.
- Tjänsten VMICTimeSync som sekundärt, som används för att kommunicera värd tiden till de virtuella datorerna och göra ändringar när den virtuella datorn har pausats för underhåll. Azure värdar använder enheter som ägs av Microsoft Stratum 1 för att hålla rätt tid.

I senare Linux-distributioner kan tjänsten VMICTimeSync använder precision time protocol (PTP), men tidigare distributioner PTP stöder inte och kommer fall – tillbaka till NTP för att hämta tid från värden.

För att bekräfta NTP synkroniseras korrekt, kör den `ntpq -p` kommando.

### <a name="host-only"></a>Endast värden 

Eftersom NTP-servrar som time.windows.com och ntp.ubuntu.com är offentlig kan kräver synkroniseras med dem skickar trafik via internet. Olika paket fördröjningar kan påverka kvaliteten på synkroniseringen. Ta bort NTP genom att växla till endast värden synkronisering kan förbättra din tid ibland synkronisera resultat.

Växla till värden endast tid synkronisering gör uppfattning om det uppstår tidssynkronisering utfärdar använder standardkonfigurationen. Prova att använda endast värden sync att se om det skulle förbättra tidssynkronisering på den virtuella datorn. 

### <a name="external-time-server"></a>Extern tidsservern

Om du har viss tidskrav för katalogsynkronisering, finns det också ett alternativ för att använda extern tidsservrar. Extern tidsservrar kan tillhandahålla viss tid, vilket kan vara användbart för testning, säkerställer tid enhetlighet med datorer som finns i icke-Microsoft-datacenter eller hantering av skottsekunder på ett speciellt sätt.

Du kan kombinera en extern tid-server med VMICTimeSync-tjänsten för att visa resultat som liknar standardkonfigurationen. Kombinera en extern tid-server med VMICTimeSync är det bästa alternativet för att hantera problem som kan vara orsaken när virtuella datorer har pausats för underhåll. 

## <a name="tools-and-resources"></a>Verktyg och resurser

Det finns vissa grundläggande kommandon för att kontrollera din konfiguration av synkronisering. Dokumentation för Linux-distribution innehåller mer information på det bästa sättet att konfigurera synkronisering av tid för distributionen.

### <a name="integration-services"></a>Integreringstjänster

Kontrollera om integration-tjänsten (hv_utils) har lästs in.

```bash
lsmod | grep hv_utils
```
Du bör se ut ungefär så här:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Kontrollera om integration services-daemon för Hyper-V körs.

```bash
ps -ef | grep hv
```

Du bör se ut ungefär så här:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>Sök efter PTP

Med nyare versioner av Linux Precision Time Protocol (PTP) klockkälla är tillgänglig som en del av VMICTimeSync-providern. Äldre versioner av Red Hat Enterprise Linux eller CentOS 7.x den [Linux Integration Services](https://github.com/LIS/lis-next) kan hämtas och används för att installera den uppdaterade drivrutinen. När du använder PTP Linux enheten kommer att av formuläret/dev/ptp*x*. 

Se vilka PTP klockan källor som är tillgängliga.

```bash
ls /sys/class/ptp
```

I det här exemplet är värdet som returneras är *ptp0*, så vi kan använda den för att kontrollera namnet på klockan. Kontrollera namnet på klockan för att verifiera enheten.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Detta bör returnera **hyperv**.

### <a name="chrony"></a>chrony

På Red Hat Enterprise Linux och CentOS 7.x och [chrony](https://chrony.tuxfamily.org/) konfigurerad för att använda en PTP källa klocka. Daemonen Network Time Protocol (ntpd) har inte stöd för PTP källor, det med hjälp av **chronyd** rekommenderas. Om du vill aktivera PTP uppdatera **chrony.conf**.

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Läs mer på Red Hat och NTP [konfigurera NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp). 

Läs mer på chrony [med chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony).

Om både chrony och TimeSync källor aktiveras samtidigt, kan du markera en som **föredrar** som anger den andra källan som en säkerhetskopia. Eftersom NTP-tjänster inte uppdateras klockan för stora jämföra utom efter en lång tid, återställer VMICTimeSync klockan från pausad Virtuella datorhändelser mycket snabbare än NTP-baserade verktyg enbart.


### <a name="systemd"></a>systemd 

I Ubuntu och SUSE tid synkronisering har konfigurerats med hjälp av [systemd](https://www.freedesktop.org/wiki/Software/systemd/). Läs mer på Ubuntu [tidssynkronisering](https://help.ubuntu.com/lts/serverguide/NTP.html). Mer information om SUSE finns i avsnittet 4.5.8 i [SUSE Linux Enterprise Server 12 SP3 viktig](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).



## <a name="next-steps"></a>Nästa steg

Mer information finns i [korrekt tid för Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time).


