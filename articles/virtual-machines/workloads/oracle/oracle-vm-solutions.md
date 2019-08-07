---
title: Oracle-lösningar på virtuella Azure-datorer | Microsoft Docs
description: Lär dig mer om konfigurationer som stöds och begränsningar för avbildningar av virtuella Oracle-datorer på Microsoft Azure.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
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
ms.openlocfilehash: f13c7e6db2bf13c0547ee8689e22c9f04fe6d511
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826760"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>VIRTUELLA Oracle-avbildningar och deras distribution på Microsoft Azure

Den här artikeln beskriver information om Oracle-lösningar baserade på avbildningar av virtuella datorer som publicerats av Oracle på Azure Marketplace. Om du är intresse rad av program lösningar över molnet med Oracle Cloud Infrastructure, se Oracle-programlösningar som [integrerar Microsoft Azure och Oracle Cloud Infrastructure](oracle-oci-overview.md).

Om du vill hämta en lista över tillgängliga avbildningar kör du följande kommando:

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

De här avbildningarna betraktas som "ta med din egen licens" och så att du bara debiteras för beräknings-, lagrings-och nätverks kostnader som uppstår genom att köra en virtuell dator.  Vi förutsätter att du är korrekt licensierad till att använda Oracle-programvara och att du har ett aktuellt support avtal på plats med Oracle. Oracle har garanterat licens mobilitet från lokal plats till Azure. Se publicerad [Oracle och Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) -anteckning för mer information om licens mobilitet. 

Individer kan också välja att basera sina lösningar på en anpassad avbildning som de skapar från grunden i Azure eller ladda upp en anpassad avbildning från sin lokala miljö.

## <a name="support-for-jd-edwards"></a>Stöd för JD-Edwards
Enligt Oracle support NOTE [dokument-ID 2178595,1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), JD Edwards EnterpriseOne-versionerna 9,2 och senare, stöds i **alla offentliga moln** som uppfyller deras speciella `Minimum Technical Requirements` (MTR).  Du måste skapa anpassade avbildningar som uppfyller MTR-specifikationerna för operativ system-och program varu kompatibilitet. 

## <a name="oracle-database-vm-images"></a>VM-avbildningar för Oracle-databasen
Oracle stöder körning av Oracle DB 12,1 standard-och Enterprise-versioner i Azure på avbildningar av virtuella datorer baserat på Oracle Linux.  För att få bästa möjliga prestanda för produktions arbets belastningar för Oracle DB på Azure måste du se till att du har rätt storlek på den virtuella dator avbildningen och använder Managed Disks som backas upp av Premium Storage. För instruktioner om hur du snabbt får en Oracle DB igång i Azure med hjälp av den virtuella Oracle-avbildningen, [provar du genom gången Oracle DB Snabb start](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Konfigurations alternativ för anslutna diskar

Anslutna diskar är beroende av Azure Blob Storage-tjänsten. Varje standard disk kan ha en teoretisk max på cirka 500 indata/utdata-åtgärder per sekund (IOPS). Vårt erbjudande för Premium disk är att föredra för databas arbets belastningar med höga prestanda och kan få upp till 5000 IOps per disk. Du kan använda en enskild disk om den uppfyller dina prestanda behov. Du kan dock förbättra den effektiva IOPS-prestandan om du använder flera anslutna diskar, sprida databas data över dem och sedan använda hantering av Oracle automatisk lagring (ASM). Se [Översikt över Oracle Automatic Storage](https://www.oracle.com/technetwork/database/index-100339.html) för mer Oracle ASM-detaljerad information. Ett exempel på hur du installerar och konfigurerar Oracle ASM på en Linux Azure VM finns i själv studie kursen [Installera och konfigurera Oracle automatisk lagrings hantering](configure-oracle-asm.md) .

### <a name="shared-storage-configuration-options"></a>Konfigurations alternativ för delad lagring

Azure NetApp Files har utformats för att uppfylla kärn kraven för att köra arbets belastningar med höga prestanda som databaser i molnet, och ger
- Azure Native Shared NFS-lagrings tjänst för att köra Oracle-arbetsbelastningar antingen via den ursprungliga virtuella datorn NFS-klienten eller Oracle dNFS
- Skalbara prestanda nivåer som återspeglar det verkliga utbudet av IOPS-krav
- Låg latens
- Hög tillgänglighet, hög hållbarhet och hanterbarhet i skala, vanligt vis efter verksamhets kritiska företags arbets belastningar (t. ex. SAP och Oracle)
- Snabb och effektiv säkerhets kopiering och återställning för att uppnå de mest aggressiva RTO och återställnings service avtalet

Dessa funktioner är möjliga eftersom Azure NetApp Files baseras på NetApp® ONTAP® alla Flash-system som körs i Azure Data Center-miljön, som en Azure Native-tjänst. Resultatet är en idealisk teknik för databas lagring som kan tillhandahållas och konsumeras precis som andra alternativ för Azure Storage. Mer information om hur du distribuerar och får åtkomst till Azure NetApp Files NFS-volymer finns i [Azure NetApp Files-dokumentationen](https://docs.microsoft.com/azure/azure-netapp-files/) . Se [metod tips för Oracle i Azure-distribution med hjälp av Azure NetApp Files](https://www.netapp.com/us/media/tr-4780.pdf) för bästa praxis för att distribuera en Oracle-databas på Azure NetApp Files.


## <a name="oracle-real-application-cluster-oracle-rac"></a>Verkligt Oracle-programkluster (Oracle RAC)
Oracle RAC är utformat för att minimera fel i en enskild nod i en lokal kluster konfiguration med flera noder. Den förlitar sig på två lokala tekniker som inte är inbyggda för storskaliga offentliga moln miljöer: nätverks-multicast och delad disk. Om din databas lösning kräver Oracle RAC i Azure, behöver du en tredje = parts program vara för att aktivera dessa tekniker. Mer information om Oracle RAC finns på [sidan FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Överväganden för hög tillgänglighet och haveri beredskap
När du använder Oracle-databaser i Azure ansvarar du för att implementera en lösning för hög tillgänglighet och haveri beredskap för att undvika avbrott. 

Hög tillgänglighet och haveri beredskap för Oracle Database Enterprise Edition (utan att förlita dig på Oracle RAC) kan uppnås på Azure med hjälp av [data Guard, Active data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)eller [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate), med två databaser på två separata virtuella faxar. Båda de virtuella datorerna bör finnas i samma [virtuella nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) för att säkerställa att de kan komma åt varandra via den privata, BEständiga IP-adressen.  Dessutom rekommenderar vi att du placerar de virtuella datorerna i samma tillgänglighets uppsättning för att tillåta Azure att placera dem i separata fel domäner och uppgraderings domäner. Vill du ha GEO-redundans ställer du in de två databaserna för replikering mellan två olika regioner och ansluter de två instanserna med en VPN Gateway.

I självstudien [implementera Oracle data Guard i Azure](configure-oracle-dataguard.md) får du stegvisa anvisningar genom den grundläggande installations proceduren i Azure.  

Med Oracle data Guard kan hög tillgänglighet uppnås med en primär databas på en virtuell dator, en sekundär databas (vänte läge) i en annan virtuell dator och enkelriktad replikering som kon figurer ATS mellan dem. Resultatet är Läs behörighet till kopian av databasen. Med Oracle GoldenGate kan du konfigurera dubbelriktad replikering mellan de två databaserna. Information om hur du konfigurerar en lösning med hög tillgänglighet för dina databaser med hjälp av dessa verktyg finns i dokumentationen om [Active data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) och [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) på Oracle-webbplatsen. Om du behöver Läs-och Skriv behörighet till kopian av databasen kan du använda [Oracle Active data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

I själv studie kursen [implementeras Oracle GoldenGate i Azure](configure-oracle-golden-gate.md) med hjälp av den grundläggande installations proceduren i Azure.

Förutom att ha en HA-och DR-lösning som är konstruerad i Azure bör du ha en säkerhets kopierings strategi på plats för att återställa databasen. I självstudien om [säkerhets kopiering och återställning av en Oracle Database](oracle-backup-recovery.md) går vi igenom den grundläggande proceduren för att upprätta en konsekvent säkerhets kopia.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Avbildningar av virtuella datorer i Oracle WebLogic-Server

* **Klustring stöds endast på Enterprise Edition.** Du är licensierad att endast använda WebLogic-kluster när du använder Enterprise-versionen av WebLogic-servern. Använd inte kluster med WebLogic Server Standard Edition.
* **UDP-multicast stöds inte.** Azure har stöd för UDP-unicast, men inte multicasting eller Broadcasting. WebLogic-servern kan förlita sig på funktioner i Azure UDP unicast. För bästa resultat som förlitar sig på UDP unicast, rekommenderar vi att kluster storleken för WebLogic hålls statisk eller hålls kvar utan fler än 10 hanterade servrar.
* **WebLogic-servern förväntar sig att offentliga och privata portar är samma för T3-åtkomst (till exempel när du använder företags-JavaBeans).** Överväg ett scenario med flera nivåer där ett Service Layer (EJB)-program körs på ett WebLogic Server kluster som består av två eller flera virtuella datorer i ett virtuellt nätverk med namnet *SLWLS*. Klient nivån finns i ett annat undernät i samma virtuella nätverk, med ett enkelt Java-program som försöker anropa EJB i tjänst skiktet. Eftersom det är nödvändigt att belastningsutjämna tjänst lagret måste en offentlig belastningsutjämnad slut punkt skapas för de virtuella datorerna i WebLogic-serverkluster. Om den privata port som du anger skiljer sig från den offentliga porten (till exempel 7006:7008) uppstår ett fel som följande inträffar:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Detta beror på att WebLogic-servern förväntar sig att belastnings Utjämnings porten och den WebLogic-hanterade Server porten ska vara densamma för en fjärr-T3-åtkomst. I föregående fall ansluter klienten till port 7006 (belastnings Utjämnings porten) och den hanterade servern lyssnar på 7008 (den privata porten). Den här begränsningen gäller endast för T3-åtkomst, inte HTTP.

   Undvik det här problemet genom att använda någon av följande lösningar:

  * Använd samma privata och offentliga port nummer för belastningsutjämnade slut punkter som är dedikerade till T3-åtkomst.
  * Ta med följande JVM-parameter när du startar WebLogic-servern:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Relaterad information finns i KB-artikel **860340,1** på <https://support.oracle.com>.

* **Begränsningar för dynamisk kluster-och belastnings utjämning.** Anta att du vill använda ett dynamiskt kluster i WebLogic-servern och exponera det genom en enda, offentlig belastningsutjämnad slut punkt i Azure. Detta kan göras så länge du använder ett fast port nummer för var och en av de hanterade servrarna (inte dynamiskt tilldelade från ett intervall) och inte startar fler hanterade servrar än det finns datorer som administratören spårar. Det finns inte fler än en hanterad server per virtuell dator. Om konfigurationen leder till att fler WebLogic servrar startas än om det finns virtuella datorer (det vill säga om flera WebLogic-instanser delar samma virtuella dator), är det inte möjligt för mer än en av dessa instanser av WebLogic-servrar för att binda till ett angivet port nummer. De andra på den virtuella datorn fungerar inte.

   Om du konfigurerar administrations servern så att den automatiskt tilldelar unika port nummer till dess hanterade servrar är det inte möjligt att använda belastnings utjämning eftersom Azure inte stöder mappning från en enda offentlig port till flera privata portar, som skulle krävas för detta inställningarna.
* **Flera instanser av WebLogic-servern på en virtuell dator.** Beroende på din distributions krav kan du överväga att köra flera instanser av WebLogic Server på samma virtuella dator, om den virtuella datorn är tillräckligt stor. Till exempel på en virtuell dator med medelhög storlek, som innehåller två kärnor, kan du välja att köra två instanser av WebLogic-servern. Vi rekommenderar dock fortfarande att du undviker att införa enskilda felpunkter i din arkitektur, vilket är fallet om du bara använde en virtuell dator som kör flera instanser av WebLogic-servern. Att använda minst två virtuella datorer kan vara en bättre metod och varje virtuell dator kan sedan köra flera instanser av WebLogic-servern. Varje instans av WebLogic-servern kan fortfarande vara en del av samma kluster. Men det är för närvarande inte möjligt att använda Azure för att belastningsutjämna slut punkter som exponeras av sådana WebLogic Server-distributioner inom samma virtuella dator, eftersom Azure Load Balancer kräver att de belastningsutjämnade servrarna distribueras mellan unika virtuella datorer.

## <a name="oracle-jdk-virtual-machine-images"></a>Avbildningar av virtuella Oracle JDK-datorer
* **JDK 6 och 7 de senaste uppdateringarna.** Även om vi rekommenderar att du använder den senaste offentliga, stödda versionen av Java (för närvarande Java 8), gör Azure också att JDK 6-och 7-avbildningar är tillgängliga. Detta är avsett för äldre program som ännu inte är klara att uppgraderas till JDK 8. Även om uppdateringar av tidigare JDK-avbildningar kanske inte längre är tillgängliga för allmänheten, med tanke på Microsoft partner med Oracle, är de JDK 6-och 7-avbildningar som tillhandahålls av Azure avsedda att innehålla en nyare icke-offentlig uppdatering som normalt erbjuds av Oracle till endast en Välj grupp av Oracle-kunder som stöds. Nya versioner av JDK-avbildningarna blir tillgängliga med tiden med uppdaterade versioner av JDK 6 och 7.

   JDK som är tillgängliga i JDK 6-och 7-avbildningarna och de virtuella datorer och avbildningar som härletts från dem kan bara användas i Azure.
* **64-bitars JDK.** De virtuella dator avbildningarna för Oracle WebLogic-servern och de virtuella Oracle JDK-avbildningarna som tillhandahålls av Azure innehåller 64-bitars versionerna av både Windows Server och JDK.

## <a name="next-steps"></a>Nästa steg
Nu har du en översikt över aktuella Oracle-lösningar baserade på avbildningar av virtuella datorer i Microsoft Azure. Nästa steg är att distribuera din första Oracle-databas på Azure.

> [!div class="nextstepaction"]
> [Skapa en Oracle-databas på Azure](oracle-database-quick-create.md)
