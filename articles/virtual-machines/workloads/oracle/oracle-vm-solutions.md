---
title: Oracle-lösningar på Azure virtual machines | Microsoft Docs
description: Läs mer om konfigurationer som stöds och begränsningar i avbildningar av virtuella datorer med Oracle på Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 9dd7f7d07b34ed3c1076b46c0bf5185d6c8cd31a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074235"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle VM-avbildningar och deras distribution på Microsoft Azure

Den här artikeln innehåller information om Oracle-lösningar som bygger på VM-avbildningar som publicerats av Oracle på Azure Marketplace. Om du är intresserad av molnöverskridande programlösningar med Oracle-Molninfrastruktur, se [Oracle-programlösningar integrera Microsoft Azure och Oracle Cloud Infrastructure](oracle-oci-overview.md).

Om du vill hämta en lista över tillgängliga avbildningar, kör du följande kommando:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

Från och med maj 2019 finns följande avbildningar:

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

Dessa avbildningar betraktas som ”Bring Your Own License” och därför bara att du debiteras för beräkning, lagring och nätverk kostnaderna genom att köra en virtuell dator.  Det förutsätts att du är korrekt licensierade om du vill använda Oracle-programvara och att du har en aktuell support-avtal med Oracle. Oracle har garanterat licensmobilitet från en lokal plats till Azure. Se det publicerade [Oracle och Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) Obs mer information om license mobility. 

Enskilda användare kan också välja att basera sina lösningar på en anpassad avbildning de skapa från grunden i Azure eller överföra en anpassad avbildning från sina på lokal miljö.

## <a name="support-for-jd-edwards"></a>Stöd för JD Edwards
Enligt Oracle-Support Obs [dokument-ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), JD Edwards EnterpriseOne version 9.2 och senare stöds på **alla offentliga molnet erbjudande** som uppfyller deras specifika `Minimum Technical Requirements` (MTR).  Du behöver skapa anpassade avbildningar som uppfyller deras MTR specifikationerna för operativsystem och programkompatibilitet. 

## <a name="oracle-database-vm-images"></a>Oracle database VM-avbildningar
Oracle ger support som kör Oracle DB 12.1 Standard eller Enterprise Edition i Azure på avbildningar av virtuella datorer baserat på Oracle Linux.  För bästa prestanda för arbetsbelastningar för produktion för Oracle DB på Azure, måste du ändra storlek på VM-avbildning och använda hanterade diskar som backas upp av Premium Storage korrekt. För instruktioner om hur du snabbt få en Oracle DB igång i Azure med hjälp av Oracle publicerad virtuell datoravbildning, [försök Oracle DB-Quickstart-genomgång](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Ansluten disk konfigurationsalternativ

Anslutna diskar är beroende av Azure Blob storage-tjänsten. Varje standard disk kan högst teoretisk cirka 500 indata/utdataåtgärder per sekund (IOPS). Vårt erbjudande för premium disk är att föredra för databasarbetsbelastningar med höga prestanda och kan uppnå upp till 5 000 IOps per disk. Du kan använda en skiva om som uppfyller dina behov. Du kan dock förbättra effektiva IOPS-prestanda om du använder flera anslutna diskar, sprida databasdata över dem och sedan använda Oracle automatisk Storage Management (ASM). Se [Oracle automatisk lagringsöversikt](https://www.oracle.com/technetwork/database/index-100339.html) mer specifik information för Oracle ASM. Ett exempel på hur du kan installera och konfigurera Oracle ASM på en Linux Azure VM finns i den [installera och konfigurera Oracle automatisk lagringshantering](configure-oracle-asm.md) självstudien.

### <a name="shared-storage-configuration-options"></a>Konfigurationsalternativ för delad lagring

Azure NetApp-filer har utformats för att uppfylla de viktigaste kraven för att köra arbetsbelastningar med höga prestanda som databaser i molnet och tillhandahåller;
- Azure intern delade NFS storage-tjänst för att köra Oracle-arbetsbelastningar antingen via VM inbyggda NFS-klienten eller Oracle dNFS
- Skalbar prestandanivåer som återspeglar verkliga intervallet av IOPS-kraven
- Låg latens
- Hög tillgänglighet, hög tillförlitlighet och hanterbarhet i skala, vanligtvis krävs av verksamhetskritiska kritiska företagsarbetsbelastningar (till exempel SAP och Oracle)
- Snabba och effektiva säkerhetskopiering och återställning för att uppnå lägsta RTO och RPO SLA

Dessa funktioner är möjligt eftersom Azure NetApp filer är baserad på NetApp ONTAP® all-flash-system som körs inom Azure datacentermiljö – som en intern Azure-tjänst. Resultatet är ett perfekt databasen lagringsteknik som kan etableras och används precis som andra Azure-lagringsalternativen. Se [Azure NetApp Files dokumentation](https://docs.microsoft.com/azure/azure-netapp-files/) mer information om hur du distribuerar och komma åt Azure NetApp filer NFS-volymer. Se [Oracle på Azure bästa praxis Guide med hjälp av Azure NetApp distributionsfiler](https://www.netapp.com/us/media/tr-4780.pdf) för rekommendationer om metodtips för att driva en Oracle-databas på Azure NetApp-filer.


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application kluster (Oracle RAC)
Oracle RAC är utformad för att minimera fel på en nod i ett lokalt kluster med flera noder konfiguration. Den förlitar sig på två lokala tekniker som inte är inbyggt i hyperskala offentliga molnmiljöer: multicast-nätverk och delad disk. Om din databaslösning kräver Oracle RAC i Azure, måste tredje = tillverkare om du vill aktivera dessa tekniker. Mer information om Oracle RAC finns i den [FlashGrid SkyCluster sidan](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Överväganden hög tillgänglighet och katastrofåterställning
När du använder Oracle-databaser i Azure kan ansvarar du för att implementera en hög tillgänglighet och katastrofåterställning lösning för att undvika driftavbrott. 

Hög tillgänglighet och katastrofåterställning återställning för Oracle Database Enterprise Edition (utan att behöva Oracle RAC) kan ske på Azure med hjälp av [Data Guard, aktiva Data Guard](https://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), eller [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate), med två databaser på två separata virtuella datorer. Båda de virtuella datorerna ska vara i samma [virtuellt nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) att se till att de kan komma åt varandra via privata beständiga IP-adress.  Vi rekommenderar dessutom att placera de virtuella datorerna i samma tillgänglighetsuppsättning så att Azure för att placera dem i separata feldomäner och uppgraderingsdomäner. Bör du ha geo-redundans, ställa in de två databaserna du replikerar mellan två olika regioner och ansluter två instanser med en VPN-Gateway.

Självstudien [implementera Oracle Data Guard på Azure](configure-oracle-dataguard.md) vägleder dig genom proceduren för grundläggande konfiguration på Azure.  

Med Oracle Data Guard, hög tillgänglighet kan ske med en primär databas i en virtuell dator, en sekundära (standby) databas i en annan virtuell dator och envägsreplikering mellan dem. Resultatet är läsbehörighet till kopia av databasen. Du kan konfigurera dubbelriktad replikering mellan de två databaserna med Oracle GoldenGate. Läs hur du ställer in en lösning för hög tillgänglighet för dina databaser med hjälp av verktygen i [Active Data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) och [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) dokumentation på Oracle-webbplatsen. Om du behöver skrivskyddad åtkomst till kopia av databasen, kan du använda [Active Oracle Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Självstudien [implementera Oracle GoldenGate på Azure](configure-oracle-golden-gate.md) vägleder dig genom proceduren för grundläggande konfiguration på Azure.

Förutom att ha en HA och DR-lösning som byggts i Azure, bör du ha en strategi för säkerhetskopiering för att återställa databasen. Självstudien [säkerhetskopiera och återställa en Oracle-databas](oracle-backup-recovery.md) vägleder dig genom den grundläggande proceduren för att upprätta en programkonsekvent säkerhetskopiering.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server-avbildningar för virtuella datorer

* **Kluster stöds på Enterprise Edition endast.** Du har licenser för att använda WebLogic klustring endast när du använder Enterprise-utgåvan av WebLogic Server. Använd inte klustring med WebLogic Server Standard Edition.
* **UDP-multicast stöds inte.** Azure har stöd för UDP unicast, men inte multicasting eller broadcasting. WebLogic Server kan förlita dig på Azure UDP unicast-funktioner. För bästa resultat som förlitar sig på UDP unicast, rekommenderar vi att klusterstorleken WebLogic sparas statiska eller behållna med mer än 10 hanterade servrar.
* **WebLogic Server förväntar sig offentliga och privata portar vara samma för T3 åtkomst (till exempel när du använder Enterprise JavaBeans).** Överväg ett scenario med flera nivåer där ett tjänstprogram layer (EJB) körs på ett WebLogic Server-kluster som består av två eller flera datorer i ett virtuellt nätverk med namnet *SLWLS*. Klient-nivån är i ett annat undernät i samma virtuella nätverk som kör en enkel Java-programmet försöker anropa EJB i lagret för tjänsten. Eftersom det är nödvändigt att belastningsutjämna service-lagret, måste en offentlig slutpunkt för Utjämning av nätverksbelastning skapas för virtuella datorer i WebLogic Server-klustret. Om den privata porten som du anger skiljer sig från den offentliga porten (till exempel 7006:7008), inträffar ett fel, till exempel följande:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Det beror på att för fjärråtkomst T3, WebLogic Server förväntar sig belastningsutjämnarporten och WebLogic hanteras serverporten ska vara samma. I föregående fall klienten har åtkomst till port 7006 (belastningsutjämnarporten) och den hanterade servern lyssnar på 7008 (privat port). Den här begränsningen gäller endast för T3 åtkomst, inte HTTP.

   Undvik problemet genom att använda någon av följande lösningar:

  * Använd samma privata och offentliga portnummer för belastningsutjämnade slutpunkter som är dedikerad till T3 åtkomst.
  * Ta med följande JVM-parameter när du startar WebLogic Server:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Mer information finns i KB-artikel **860340.1** på <https://support.oracle.com>.

* **Dynamisk klustring och begränsningar för belastningsutjämning.** Anta att du vill använda en dynamisk kluster i WebLogic Server och gör den tillgänglig via en enskild, offentlig belastningsutjämnad slutpunkt i Azure. Detta kan göras så länge som du använder ett fast portnummer för var och en av de hanterade servrarna (inte dynamiskt tilldelade från ett intervall) och startar inte mer hanterade servrar än det finns datorer som administratören för att spåra. Det är mer än en hanterad server per virtuell dator). Om din konfiguration resulterar i fler WebLogic servrar ha startats än det finns virtuella datorer (det vill säga där flera WebLogic Server-instanser delar samma virtuella dator), och det inte går för fler än en av dessa instanser av WebLogic-servrar att binda till en viss portnummer. De andra på den virtuella datorn misslyckas.

   Om du konfigurerar administrationsserver för att automatiskt tilldela unika portnummer för de hanterade servrarna, sedan kan Utjämning av nätverksbelastning inte utföras eftersom Azure inte stöder mappningen från en enda offentlig port till flera privata portar som krävs för den här konfiguration.
* **Flera instanser av WebLogic Server på en virtuell dator.** Beroende på distributionskraven kan du köra flera instanser av WebLogic Server på samma virtuella dator, om den virtuella datorn är tillräckligt stor. På en medelstora virtuella dator som innehåller två kärnor, kan du till exempel välja att köra två instanser av WebLogic Server. Men rekommenderar vi ändå att du undviker att införa enskilda felpunkter i arkitekturen, vilket skulle vara fallet om du använder bara en virtuell dator som kör flera instanser av WebLogic Server. Med hjälp av minst två virtuella datorer kan vara en bättre metod och varje virtuell dator kan sedan köra flera instanser av WebLogic Server. Varje instans av WebLogic-Server kan fortfarande vara en del av samma kluster. Det är dock för närvarande inte möjligt att använda Azure för att belastningsutjämna slutpunkter som exponeras av sådana WebLogic Server-distributioner inom samma virtuella dator, eftersom Azure-belastningsutjämnaren kräver Utjämning av nätverksbelastning-servrar som ska distribueras mellan unika virtuella datorer.

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle-avbildningar för virtuella datorer av JDK
* **JDK 6 och 7 senaste uppdateringarna.** Medan vi rekommenderar att du använder den senaste offentlig, stöds versionen av Java (för närvarande Java 8), tillgängliggör Azure också JDK 6 och 7 bilder. Detta är avsett för äldre program som inte ännu är redo att uppgraderas till JDK 8. När uppdateringar till föregående JDK-avbildningar kan inte längre vara tillgänglig för allmänheten, givet Microsoft-partnerskap med Oracle, är JDK 6 och 7 avbildningar som tillhandahålls av Azure avsedda att innehålla en senare icke-offentlig uppdatering som erbjuds normalt i Oracle till endast en viss grupp med Oracle stöds kunder. Nya versioner av JDK-avbildningar kommer att göras tillgängligt över tid med uppdaterade versioner av JDK 6 och 7.

   JDK som är tillgängliga i JDK 6 och 7 avbildningar och virtuella datorer och bilder som har härletts från dem, kan bara användas i Azure.
* **64-bitars JDK.** Oracle WebLogic Server-avbildningar och Oracle JDK-VM-avbildningarna som tillhandahålls av Azure innehåller 64-bitarsversioner av Windows Server- och JDK-Paketet.

## <a name="next-steps"></a>Nästa steg
Nu har du en översikt över aktuella Oracle-lösningar som bygger på avbildningar av virtuella datorer i Microsoft Azure. Nästa steg är att distribuera din första Oracle-databas på Azure.

> [!div class="nextstepaction"]
> [Skapa en Oracle-databas på Azure](oracle-database-quick-create.md)
