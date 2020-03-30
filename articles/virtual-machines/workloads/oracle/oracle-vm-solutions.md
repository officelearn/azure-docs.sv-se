---
title: Oracle-lösningar på virtuella Azure-datorer | Microsoft-dokument
description: Lär dig mer om konfigurationer och begränsningar för Oracles virtuella datoravbildningar på Microsoft Azure som stöds.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 3abc09f8c82442e3b24a9edf6ef4fb42f19dfde8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806957"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle VM-avbildningar och deras distribution på Microsoft Azure

Den här artikeln innehåller information om Oracle-lösningar baserat på avbildningar från virtuella datorer som publicerats av Oracle på Azure Marketplace. Om du är intresserad av programlösningar över moln med Oracles molninfrastruktur läser [du Oracles programlösningar som integrerar Microsoft Azure och Oracles molninfrastruktur](oracle-oci-overview.md).

Om du vill hämta en lista över tillgängliga bilder kör du följande kommando:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Från och med maj 2019 finns följande bilder tillgängliga:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Dessa avbildningar anses vara "Ta med din egen licens" och som sådan debiteras du endast för beräknings-, lagrings- och nätverkskostnader som uppstår genom att köra en virtuell dator.  Det antas att du är korrekt licensierad att använda Oracle-programvara och att du har ett aktuellt supportavtal på plats med Oracle. Oracle har garanterad licensmobilitet från lokalt till Azure. Mer information om licensmobilitet finns i den publicerade [Oracle- och](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) Microsoft-anteckningen. 

Enskilda personer kan också välja att basera sina lösningar på en anpassad avbildning som de skapar från grunden i Azure eller ladda upp en anpassad avbildning från sin lokala miljö.

## <a name="oracle-database-vm-images"></a>Vm-avbildningar för Oracle-databas
Oracle stöder att oracle-databas 12.1 och högre standard- och enterprise-utgåvor körs i Azure på avbildningar baserade på virtuella datorer baserat på Oracle Linux.  För bästa prestanda för produktionsarbetsbelastningar för Oracle Database på Azure, se till att korrekt storlek vm avbildningen och använda Premium SSD eller Ultra SSD Managed Disks. Instruktioner om hur du snabbt får igång en Oracle-databas i Azure med den publicerade VM-avbildningen i Oracle kan [du prova snabbstartsgenomgången för Oracle-databasen](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Alternativ för bifogad diskkonfiguration

Anslutna diskar förlitar sig på Azure Blob-lagringstjänsten. Varje standarddisk klarar av ett teoretiskt maximum på cirka 500 in-/utdataoperationer per sekund (IOPS). Vårt premiumdiskerbjudande är att föredra för högpresterande databasarbetsbelastningar och kan uppnå upp till 5000 IOps per disk. Du kan använda en enda disk om det uppfyller dina prestandabehov. Du kan dock förbättra den effektiva IOPS-prestandan om du använder flera anslutna diskar, sprider databasdata över dem och sedan använder Oracle Automatic Storage Management (ASM). Se [översikt över automatisk lagring i Oracle](https://www.oracle.com/technetwork/database/index-100339.html) för mer Oracle ASM-specifik information. Ett exempel på hur du installerar och konfigurerar Oracle ASM på en virtuell Linux Azure-dator finns i självstudien [Installera och konfigurera Oracle Automated Storage Management.](configure-oracle-asm.md)

### <a name="shared-storage-configuration-options"></a>Konfigurationsalternativ för delad lagring

Azure NetApp Files har utformats för att uppfylla de grundläggande kraven för att köra högpresterande arbetsbelastningar som databaser i molnet och tillhandahåller.
- Azure native delad NFS-lagringstjänst för att köra Oracle-arbetsbelastningar antingen via inbyggd NFS-klient för virtuella datorer eller Oracle dNFS
- Skalbara prestandanivåer som återspeglar det verkliga utbudet av IOPS-krav
- Låg latens
- Hög tillgänglighet, hög hållbarhet och hanterbarhet i stor skala, vanligtvis efterfrågas av verksamhetskritiska företagsarbetsbelastningar (som SAP och Oracle)
- Snabb och effektiv säkerhetskopiering och återställning, för att uppnå de mest aggressiva RTO- och RPO-säkerhetsassss

Dessa funktioner är möjliga eftersom Azure NetApp-filer baseras på NetApp® ONTAP® all-flash-system som körs i Azure-datacentermiljö – som en Azure Native-tjänst. Resultatet är en idealisk databaslagringsteknik som kan etableras och förbrukas precis som andra Azure-lagringsalternativ. Mer information om hur du distribuerar och får åtkomst till Azure NetApp Files NFS-volymer finns i Dokumentationen till Azure [NetApp Files.](https://docs.microsoft.com/azure/azure-netapp-files/) Se [Oracle på Azure Deployment Best Practice Guide Använda Azure NetApp-filer](https://www.netapp.com/us/media/tr-4780.pdf) för rekommendationer om bästa praxis för att driva en Oracle-databas på Azure NetApp-filer.


## <a name="licensing-oracle-database--software-on-azure"></a>Licensiering av Oracle Database & programvara på Azure
Microsoft Azure är en auktoriserad molnmiljö för att köra Oracle Database. Oracle Core Factor-tabellen är inte tillämplig när Oracle-databaser licensieras i molnet. När du använder virtuella datorer med teknik för hypertrådsteknik aktiverad för Enterprise Edition-databaser räknar du i stället två virtuella processorer som likvärdiga med en Oracle-processorlicens om hypertrådning är aktiverad (som anges i principdokumentet). Policydetaljer hittar du [här](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf).
Oracle-databaser kräver i allmänhet högre minne och I/O. Därför rekommenderas [minnesoptimerade virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) för dessa arbetsbelastningar. För att optimera dina arbetsbelastningar ytterligare rekommenderas [begränsade virtuella core-processorer](https://docs.microsoft.com/azure/virtual-machines/linux/constrained-vcpu) för Oracle-databasarbetsbelastningar som kräver hög minnes-, lagrings- och I/O-bandbredd, men inte ett högt kärnantal.

När Oracle migrerar programvara och arbetsbelastningar från Oracle till Microsoft Azure tillhandahåller Oracle licensmobilitet enligt vanliga frågor om [Oracle på Azure](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle Real Application Cluster (Oracle RAC) är utformat för att minska felet för en enda nod i en lokal klusterkonfiguration för flera noder. Den är beroende av två lokala tekniker som inte är inbyggda i hyperskalliga offentliga molnmiljöer: nätverksspecifika och delade diskar. Om databaslösningen kräver Oracle RAC i Azure behöver du programvara från tredje och annan part för att aktivera dessa tekniker. Mer information om Oracle RAC finns på [sidan FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Hög tillgänglighet och katastrofåterställning
När du använder Oracle-databaser i Azure ansvarar du för att implementera en lösning för hög tillgänglighet och haveriberedskap för att undvika driftstopp. 

Hög tillgänglighet och haveriberedskap för Oracle Database Enterprise Edition (utan att förlita sig på Oracle RAC) kan uppnås på Azure med [Data Guard, Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)eller Oracle [GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate), med två databaser på två separata virtuella datorer. Båda virtuella datorer bör vara i samma [virtuella nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) för att säkerställa att de kan komma åt varandra via den privata beständiga IP-adressen.  Dessutom rekommenderar vi att du placerar de virtuella datorerna i samma tillgänglighetsuppsättning så att Azure kan placera dem i separata feldomäner och uppgradera domäner. Om du vill ha geo-redundans, ställa in de två databaserna för att replikera mellan två olika regioner och ansluta de två instanserna med en VPN Gateway.

Självstudien [Implementera Oracle Data Guard på Azure](configure-oracle-dataguard.md) hjälper dig att använda den grundläggande inställningsproceduren på Azure.  

Med Oracle Data Guard kan hög tillgänglighet uppnås med en primär databas i en virtuell dator, en sekundär databas (vänteläge) i en annan virtuell dator och envägsreplikering som ställts in mellan dem. Resultatet är läsåtkomst till kopian av databasen. Med Oracle GoldenGate kan du konfigurera dubbelriktad replikering mellan de två databaserna. Mer information om hur du konfigurerar en lösning med hög tillgänglighet för dina databaser med hjälp av dessa verktyg finns i [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) och [GoldenGate-dokumentationen](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) på Oracles webbplats. Om du behöver läs-skriv-åtkomst till kopian av databasen kan du använda [Oracle Active Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Självstudien [Implementera Oracle GoldenGate på Azure](configure-oracle-golden-gate.md) hjälper dig att använda den grundläggande inställningsproceduren på Azure.

Förutom att ha en HA- och DR-lösning utformad i Azure bör du ha en strategi för säkerhetskopiering för att återställa databasen. Självstudien [Säkerhetskopiering och återställa en Oracle-databas](oracle-backup-recovery.md) går igenom den grundläggande proceduren för att upprätta en konsekvent säkerhetskopiering.


## <a name="support-for-jd-edwards"></a>Stöd för JD Edwards
Enligt Oracle Support note [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)stöds JD Edwards EnterpriseOne-versionerna 9.2 `Minimum Technical Requirements` och senare på alla offentliga **molnerbjudanden** som uppfyller deras specifika (MTR).  Du måste skapa anpassade avbildningar som uppfyller deras MTR-specifikationer för OS- och programkompatibilitet. 


## <a name="oracle-weblogic-server-virtual-machine-images"></a>Bilder av virtuella datorer i Oracle WebLogic Server

* **Klustring stöds endast på Enterprise Edition.** Du har licens att bara använda WebLogic-kluster när du använder Enterprise Edition av Oracle WebLogic Server. Använd inte klustring med Oracle WebLogic Server Standard Edition.
* **UDP multicast stöds inte.** Azure stöder UDP-unicasting, men inte multicasting eller sändning. Oracle WebLogic Server kan förlita sig på Azure UDP unicast-funktioner. För bästa resultat förlitar sig på UDP unicast rekommenderar vi att WebLogic-klusterstorleken hålls statisk eller hålls med högst 10 hanterade servrar.
* **Oracle WebLogic Server förväntar sig att offentliga och privata portar ska vara desamma för T3-åtkomst (till exempel när du använder Enterprise JavaBeans).** Överväg ett scenario på flera nivåer där ett EJB-program (Service Layer) körs i ett Oracle WebLogic Server-kluster som består av två eller flera virtuella datorer i ett virtuellt nätverk med namnet *SLWLS*. Klientnivån finns i ett annat undernät i samma virtuella nätverk och kör ett enkelt Java-program som försöker anropa EJB i tjänstlagret. Eftersom det är nödvändigt att läsa in servicelagret måste en offentlig belastningsbalanserad slutpunkt skapas för de virtuella datorerna i Oracle WebLogic Server-klustret. Om den privata porten som du anger skiljer sig från den offentliga porten (till exempel 7006:7008), uppstår ett fel som följande:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Detta beror på att för alla fjärr-T3-åtkomst förväntar sig Oracle WebLogic Server att belastningsutjämnaren och WebLogic-hanterade serverporten ska vara densamma. I föregående fall är klienten tillgång till port 7006 (belastningsutjämnad port) och den hanterade servern lyssnar på 7008 (den privata porten). Den här begränsningen gäller endast för T3-åtkomst, inte HTTP.

   Undvik det här problemet genom att använda någon av följande lösningar:

  * Använd samma privata och offentliga portnummer för belastningsbalanserade slutpunkter som är avsedda för T3-åtkomst.
  * Inkludera följande JVM-parameter när Oracle WebLogic Server startas:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Relaterad information finns i KB artikel **860340.1** på <https://support.oracle.com>.

* **Dynamiska kluster- och belastningsutjämningsbegränsningar.** Anta att du vill använda ett dynamiskt kluster i Oracle WebLogic Server och exponera det via en enda, offentlig belastningsbalanserad slutpunkt i Azure. Detta kan göras så länge du använder ett fast portnummer för var och en av de hanterade servrarna (inte dynamiskt tilldelade från ett intervall) och inte startar fler hanterade servrar än det finns datorer som administratören spårar. Det vill än, det finns inte mer än en hanterad server per virtuell dator). Om konfigurationen resulterar i att fler Oracle WebLogic-servrar startas än det finns virtuella datorer (det vill sagt, där flera Oracle WebLogic Server-instanser delar samma virtuella dator) är det inte möjligt för mer än en av dessa instanser av Oracle WebLogic-servrar för att binda till ett visst portnummer. De andra på den virtuella datorn misslyckas.

   Om du konfigurerar administratörsservern för att automatiskt tilldela unika portnummer till sina hanterade servrar, är belastningsutjämning inte möjligt eftersom Azure inte stöder mappning från en enda offentlig port till flera privata portar, vilket krävs för detta Konfiguration.
* **Flera instanser av Oracle WebLogic Server på en virtuell dator.** Beroende på distributionens krav kan du överväga att köra flera instanser av Oracle WebLogic Server på samma virtuella dator, om den virtuella datorn är tillräckligt stor. På en virtuell dator med medelstor storlek, som innehåller två kärnor, kan du till exempel välja att köra två instanser av Oracle WebLogic Server. Vi rekommenderar dock fortfarande att du undviker att införa enkla felpunkter i din arkitektur, vilket skulle vara fallet om du bara använde en virtuell dator som kör flera instanser av Oracle WebLogic Server. Att använda minst två virtuella datorer kan vara en bättre metod, och varje virtuell dator kan sedan köra flera instanser av Oracle WebLogic Server. Varje instans av Oracle WebLogic Server kan fortfarande ingå i samma kluster. Det är dock för närvarande inte möjligt att använda Azure för att belastningsbalansslutpunkter som exponeras av sådana Oracle WebLogic Server-distributioner inom samma virtuella dator, eftersom Azure-belastningsutjämnar kräver att belastningsbalanserade servrar distribueras mellan unika virtuella datorer.

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK virtuella dator bilder
* **JDK 6 och 7 senaste uppdateringarna.** Även om vi rekommenderar att du använder den senaste offentliga versionen av Java som stöds (för närvarande Java 8), gör Azure också JDK 6 och 7 avbildningar tillgängliga. Detta är avsett för äldre program som ännu inte är redo att uppgraderas till JDK 8. Även om uppdateringar av tidigare JDK-avbildningar kanske inte längre är tillgängliga för allmänheten, med tanke på Microsofts partnerskap med Oracle, är JDK 6- och 7-avbildningarna som tillhandahålls av Azure avsedda att innehålla en nyare icke-offentlig uppdatering som normalt erbjuds av Oracle endast en utvald grupp av Oracles kunder som stöds. Nya versioner av JDK-bilderna kommer att göras tillgängliga över tid med uppdaterade utgåvor av JDK 6 och 7.

   JDK-bilderna som är tillgängliga i JDK 6- och 7-avbildningarna och de virtuella datorer och avbildningar som härleds från dem kan endast användas i Azure.
* **64-bitars JDK.** De virtuella datoravbildningarna för Oracle WebLogic Server och avbildningarna för den virtuella datorn Oracle JDK som tillhandahålls av Azure innehåller 64-bitarsversionerna av både Windows Server och JDK.

## <a name="next-steps"></a>Nästa steg
Du har nu en översikt över aktuella Oracle-lösningar baserade på avbildningar för virtuella datorer i Microsoft Azure. Nästa steg är att distribuera din första Oracle-databas på Azure.

> [!div class="nextstepaction"]
> [Skapa en Oracle-databas på Azure](oracle-database-quick-create.md)
