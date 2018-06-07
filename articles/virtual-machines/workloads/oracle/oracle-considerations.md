---
title: Oracle-lösningar för Microsoft Azure | Microsoft Docs
description: Läs mer om konfigurationer som stöds och begränsningar för Oracle-lösningar för Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
manager: jeconnoc
author: rickstercdn
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: rclaus
ms.openlocfilehash: daed709b4b4be87ba75f5539bd31c666b3a37414
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34656354"
---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Oracle-lösningar och deras distribution på Microsoft Azure
Den här artikeln innehåller information som krävs för att distribuera olika Oracle-lösningar för Microsoft Azure. Dessa lösningar baseras på virtuella avbildningar publicerade av Oracle i Azure Marketplace. Om du vill hämta en lista över tillgängliga avbildningar, kör du följande kommando:
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
Från och med 6 – 16-2017 listan över bilder är följande:
```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Linux            Oracle       6.4                     Oracle:Oracle-Linux:6.4:6.4.20141206                         6.4.20141206
Oracle-Linux            Oracle       6.7                     Oracle:Oracle-Linux:6.7:6.7.20161007                         6.7.20161007
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20161020                         6.8.20161020
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20170406                         6.9.20170406
Oracle-Linux            Oracle       7.0                     Oracle:Oracle-Linux:7.0:7.0.20141217                         7.0.20141217
Oracle-Linux            Oracle       7.2                     Oracle:Oracle-Linux:7.2:7.2.20161020                         7.2.20161020
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20170320                         7.3.20170320
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Dessa avbildningar betraktas som ”Bring Your Own License” och som sådan endast debiteras du för bearbetning, lagring och nätverk kostnaderna genom att köra en virtuell dator.  Det förutsätts att du korrekt licens för att använda Oracle-programvara och att du har en aktuell supportavtal på plats med Oracle. Oracle har garanteras licensera mobility från lokal till Azure. Finns det publicerade [Oracle och Microsoft](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) Obs mer information om licensmobilitet. 

Enskilda användare kan också välja att basera sina lösningar på en anpassad avbildning de skapa från grunden i Azure eller ladda upp en anpassad avbildning från deras på lokala miljö.

## <a name="support-for-jd-edwards"></a>Stöd för JD Edwards
Enligt stöd för Oracle Obs [Doc-ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4) , JD Edwards EnterpriseOne version 9.2 och senare stöds i **alla offentliga molntjänster erbjudande** som uppfyller specifikt `Minimum Technical Requirements` (MTR).  Du behöver skapa anpassade avbildningar som uppfyller deras MTR specifikationerna för operativsystem och programvara programkompatibilitet. 

## <a name="oracle-database-virtual-machine-images"></a>Oracle Database avbildningar av virtuella datorer
Oracle stöder körs Oracle DB 12.1 Standard och Enterprise-versioner i Azure på avbildningar av virtuella datorer baserat på Oracle Linux.  För bästa prestanda för produktionsarbetsbelastningar av Oracle-databas på Azure, måste du rätt storlek VM-avbildning och använda hanterade diskar som backas upp av Premium-lagring. För instruktioner om hur du snabbt en Oracle-databas körs i Azure med hjälp av Oracle publicerade VM-avbildning [försök Oracle DB Quickstart genomgången](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Ansluten disk konfigurationsalternativ

Anslutna diskar förlitar sig på tjänsten Azure Blob storage. Varje disk som standard kan teoretiskt maximalt cirka 500 i/o-åtgärder per sekund (IOPS). Vår premium disk erbjudande är att föredra för högpresterande arbetsbelastningar och kan få upp till 5 000 IOps per disk. Medan du kan använda en enda disk om som uppfyller dina prestandabehov - du kan förbättra effektiva IOPS prestanda om du använder flera anslutna diskar, fördelade databasdata på dem och sedan använda Oracle automatisk Storage Management (ASM). Se [automatiska Oracle-lagring – översikt](http://www.oracle.com/technetwork/database/index-100339.html) mer Oracle ASM specifik information. Ett exempel på hur du installerar och konfigurerar Oracle ASM på en Linux Azure VM - försök i [installera och konfigurera Oracle Automated lagringshantering](configure-oracle-asm.md) kursen.

## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle RAC är utformat för att minimera fel på en enskild nod i ett kluster med flera noder lokala-konfiguration. Den förlitar sig på två lokala tekniker som inte är inbyggt i storskaliga offentliga molnmiljöer: multicast-nätverket och delad disk. Om din databaslösning kräver Oracle RAC i Azure, måste 3 tillverkare för att aktivera dessa tekniker.  En **Microsoft Azure-certifierad** erbjudande kallas [FlashGrid nod för Oracle RAC](https://azuremarketplace.microsoft.com/marketplace/apps/flashgrid-inc.flashgrid-racnode?tab=Overview) är tillgängliga i Azure Marketplace publicerad av FlashGrid Inc. Mer information om den här lösningen och hur det fungerar i Azure finns i [FlashGrid lösning sidan](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Hög tillgänglighet och katastrofåterställning överväganden
När du använder Oracle-databaser i Azure måste ansvarar du för att implementera en hög tillgänglighet och haveriberedskap lösning för att undvika driftavbrott. 

Hög tillgänglighet och katastrofåterställning återställning för Oracle Database Enterprise Edition (utan Oracle RAC) kan ske på Azure med hjälp av [Data Guard, aktiva Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), eller [Oracle guld Gate](http://www.oracle.com/technetwork/middleware/goldengate), med två databaser på två separata virtuella datorer. Både virtuella datorer måste vara i samma [virtuellt nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) så de har åtkomst till varandra via privata beständiga IP-adress.  Vi rekommenderar dessutom att placera de virtuella datorerna i samma tillgänglighetsuppsättning så att Azure för att placera dem i separata feldomäner och uppgradera domäner.  Du kan ha två databaserna replikera mellan två olika regioner och ansluta två instanser med en VPN-Gateway bör du vill ha geo-redundans.

Vi har en självstudiekurs ”[implementera Oracle DataGuard på Azure](configure-oracle-dataguard.md)”, som vägleder dig genom proceduren för grundläggande konfiguration till test detta på Azure.  

Med Oracle Data Guard hög tillgänglighet kan uppnås med en primär databas i en virtuell dator, en sekundär (standby) databas i en annan virtuell dator och konfigurera mellan dem replikering. Resultatet är läsbehörighet till kopia av databasen. Du kan konfigurera dubbelriktad replikering mellan de två databaserna med Oracle GoldenGate. Information om hur du ställer in en lösning för hög tillgänglighet för dina databaser med hjälp av dessa verktyg finns [aktiva Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) och [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) dokumentation på Oracle-webbplatsen. Om du behöver skrivskyddad åtkomst till en kopia av databasen, kan du använda [Active Oracle Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Vi har en självstudiekurs ”[implementera Oracle GoldenGate på Azure](configure-oracle-golden-gate.md)”, som vägleder dig genom proceduren för grundläggande konfiguration till test detta på Azure.

Trots att ha en lösning för hög tillgänglighet och Katastrofåterställning konstruerad i Azure som du vill kontrollera att du har en strategi för säkerhetskopiering för att återställa databasen.  Vi har en självstudiekurs [säkerhetskopiering och Återställ en Oracle-databas](oracle-backup-recovery.md) som vägleder dig genom de grundläggande proceduren för att upprätta en konsekvent säkerhetskopiering.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server avbildningar av virtuella datorer
* **Kluster stöds på Enterprise Edition endast.** Du är licensierad för att använda WebLogic kluster när du använder Enterprise Edition av WebLogic Server. Använd inte kluster med WebLogic Server Standard Edition.
* **UDP-multicast stöds inte.** Azure stöder UDP unicast, men inte multicasting eller broadcasting. WebLogic Server kan förlitar sig på Azure UDP unicast-funktioner. För bästa resultat förlita dig på UDP unicast, rekommenderar vi klusterstorleken WebLogic hållas statisk, eller hållas med fler än 10 hanterade servrar som ingår i klustret.
* **WebLogic Server förväntar offentliga och privata portar ska vara samma för T3 åtkomst (till exempel när du använder Enterprise JavaBeans).** Överväg ett scenario med flera nivåer där ett tjänstprogram layer (EJB) körs på ett serverkluster med WebLogic som består av två eller flera virtuella datorer i ett vNet med namnet **SLWLS**. Klient-nivån är i ett annat undernät i samma virtuella nätverk, köra ett enkelt Java-program som försöker anropa EJB i lagret för tjänsten. Eftersom det är nödvändigt att belastningsutjämna service lagret, måste en offentlig slutpunkt för Utjämning av nätverksbelastning skapas för de virtuella datorerna i WebLogic Server-klustret. Om den privata porten som du anger skiljer sig från den offentliga porten (till exempel 7006:7008), inträffar ett fel, till exempel följande:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Det beror på att för fjärråtkomst T3, WebLogic Server förväntar sig belastningen belastningsutjämnaren port och WebLogic hanteras serverporten ska vara samma. I föregående fall klienten har åtkomst till port 7006 (belastningen belastningsutjämnaren port) och den hanterade servern lyssnar på 7008 (privat port). Den här begränsningen gäller endast för T3 åtkomst, inte HTTP.

   Om du vill undvika det här problemet använder du någon av följande lösningar:

  * Använda samma privata och offentliga portnumren för Utjämning av nätverksbelastning slutpunkter som är dedikerad till T3 åtkomst.
  * Ta med följande JVM-parameter när du startar WebLogic Server:

         -Dweblogic.rjvm.enableprotocolswitch=true

Mer information finns i KB-artikel **860340.1** på <http://support.oracle.com>.

* **Dynamisk kluster och begränsningar för belastningsutjämning.** Anta att du vill använda en dynamisk klustret i WebLogic Server och exponera den via en enskild, offentlig belastningsutjämnade slutpunkt i Azure. Detta kan göras så länge som du använder ett fast portnummer för var och en av de hanterade servrarna (inte dynamiskt tilldelade från ett intervall) och starta inte mer hanterade servrar än det finns datorer som administratören för att spåra (som är mer än en hanterad server per virtuell m achine). Om din konfiguration resulterar i fler WebLogic servrar startas än det finns virtuella datorer (det vill säga där flera WebLogic serverinstanser dela samma virtuella dator), är inte möjligt för mer än en av de instanserna av WebLogic servrar Om du vill binda ett visst portnummer – misslyckas andra på den virtuella datorn.

   Om du konfigurerar admin-servern för att automatiskt tilldela unika portnummer för dess hanterade servrar sedan är belastningsutjämning inte möjligt eftersom Azure inte stöder mappning från en offentlig port till flera privata portar som krävs för den här konfiguration.
* **Flera instanser av Weblogic Server på en virtuell dator.** Beroende på distributionskraven kan kan du välja att köra flera instanser av WebLogic Server på samma virtuella dator, om den virtuella datorn är tillräckligt stor. Till exempel på en normal storlek virtuell dator som innehåller två kärnor, kan du köra två instanser av WebLogic Server. Observera dock att fortfarande rekommenderar vi att du inte införa enskilda felpunkter i din arkitektur som skulle vara fallet om du använder en virtuell dator som kör flera instanser av WebLogic Server. Med hjälp av minst två virtuella datorer kan vara en bättre metod och var och en av de virtuella datorerna kan sedan köra flera instanser av WebLogic Server. Var och en av dessa instanser av WebLogic Server gick fortfarande inte ingå i samma kluster. Obs!, men det går för närvarande inte att använda Azure att belastningsutjämna slutpunkter som exponeras av distribueringar WebLogic Server i samma virtuella dator eftersom Azure belastningsutjämnare kräver belastningsutjämnade servrarna som ska distribueras till unika virtuella datorer.

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK avbildningar av virtuella datorer
* **JDK 6 och 7 senaste uppdateringarna.** Medan vi rekommenderar att du använder den senaste offentlig, stöds versionen av Java (för närvarande Java 8) kan tillgängliggör Azure också JDK 6 och 7 bilder. Detta är avsett för äldre program som inte ännu är redo att uppgraderas till JDK 8. När uppdateringar till föregående JDK-avbildningar kan inte längre vara tillgängliga för allmänheten, givet Microsoft tillsammans med Oracle, ska JDK-6 och 7 bilder som tillhandahålls av Azure innehålla en senare uppdatering för icke-offentliga som erbjuds normalt i Oracle till endast en viss grupp med Oracle stöds kunder. Nya versioner av JDK-bilder ska göras tillgänglig över tid med uppdaterade versioner av JDK 6 och 7.

   JDK som är tillgängliga i den här JDK 6 och 7 avbildningar och virtuella datorer och bilder som har härletts från dem, kan endast användas i Azure.
* **64-bitars JDK.** Oracle WebLogic Server virtuella avbildningar och Oracle JDK virtuella avbildningar som tillhandahålls av Azure innehåller 64-bitarsversioner av Windows Server- och JDK.

## <a name="next-steps"></a>Nästa steg
Nu har du en översikt över aktuella Oracle-lösningar för Microsoft Azure. Nästa steg är att distribuera din första Oracle-databas på Azure.
- Försök i [skapa en Oracle-databas på Azure](oracle-database-quick-create.md) kursen och kom igång.