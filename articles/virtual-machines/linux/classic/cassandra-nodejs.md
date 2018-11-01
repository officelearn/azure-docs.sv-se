---
title: Köra ett Cassandra-kluster på Linux i Azure från Node.js
description: Hur du kör ett Cassandra-kluster på Linux i Azure virtuella datorer från en Node.js-app
services: virtual-machines-linux
documentationcenter: nodejs
author: craigshoemaker
manager: routlaw
editor: ''
tags: azure-service-management
ms.assetid: 30de1f29-e97d-492f-ae34-41ec83488de0
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: cshoe
ms.openlocfilehash: 3066da9a492fc12dd8b333a089b8aabbbb647414
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50421364"
---
# <a name="run-a-cassandra-cluster-on-linux-in-azure-with-nodejs"></a>Köra ett Cassandra-kluster på Linux i Azure med Node.js

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Se Resource Manager-mallar för [Datastax Enterprise](https://azure.microsoft.com/documentation/templates/datastax) och [Spark-kluster och Cassandra på CentOS](https://azure.microsoft.com/documentation/templates/spark-and-cassandra-on-centos/).

## <a name="overview"></a>Översikt
Microsoft Azure är en öppen molnplattform som kör både Microsoft och icke-Microsoft-programvara som innehåller operativsystem, programservrar, meddelanden mellanprogram samt SQL och NoSQL-databaser från båda modellerna för kommersiella öppen källkod. Att skapa elastiska tjänster på offentliga moln, inklusive Azure kräver noggrann planering och arkitektur för avsiktlig för båda programservrarna som väl storage lager. Cassandras distribuerade lagringsarkitektur hjälper naturligt att skapa högtillgängliga system som är feltoleranta för klustret. Cassandra är en NoSQL-databas som underhålls av Apache Software Foundation på cassandra.apache.org för molnskala. Cassandra är skriven i Java. Så körs på både på Windows och Linux-plattformar.

Fokus i den här artikeln är att visa Cassandra-distribution på Ubuntu som ett enda och flera Datacenter-kluster som använder Azure Virtual Machines och virtuella nätverk. Klusterdistribution för optimerad produktionsarbetsbelastningar ligger utanför omfånget för den här artikeln eftersom den kräver flera disk nodkonfiguration, lämplig ring topologi utformning och datamodeller för att stödja nödvändiga replikering, datakonsekvens, genomströmning och hög kraven på tillgänglighet.

Den här artikeln tar en grundläggande metod för att visa vad ingår i att skapa klustret Cassandra jämfört med Docker, Chef eller Puppet som kan göra distributionen av infrastruktur mycket enklare.  

## <a name="the-deployment-models"></a>Distributionsmodellerna
Microsoft Azure-nätverk kan distributionen av isolerade privata kluster åtkomst som kan vara begränsad till att uppnå de detaljerade nätverkssäkerhet.  Eftersom den här artikeln om hur du visar Cassandra-distribution på en grundläggande nivå fokusera den inte på konsekvensnivån och optimal lagringsdesignen för dataflöde. Här är listan över nätverkskraven för hypotetiska klustret:

* Externa system har inte åtkomst till Cassandra-databas från inom eller utanför Azure
* Cassandra-klustret måste vara bakom en belastningsutjämnare för thrift-trafik
* Distribuera Cassandra-noder i två grupper i varje Datacenter för en förbättrad kluster-tillgänglighet
* Låsa klustret så som endast application server-grupp har åtkomst till databasen direkt
* Inga offentliga nätverk slutpunkter än SSH
* Varje Cassandra-klusternod måste ha en fast interna IP-adress

Cassandra kan distribueras till en enda Azure-region eller till flera regioner baserat på distribuerade natur i arbetsbelastningen. Du kan använda en modell för distribution i flera regioner för att betjäna slutanvändarna närmare till ett visst geografiskt område via samma Cassandra-infrastrukturen. Cassandras inbyggda noden replikering tar hand om synkronisering av multimaster skriver kommer från flera datacenter och anger en enhetlig vy över data till program. Distribution i flera regioner kan också hjälpa till med riskerna för bredare avbrott i Azure-tjänsten. Cassandras finjusterbar konsekvens och replikeringstopologin hjälper till att uppfylla olika RPO behov av program.

### <a name="single-region-deployment"></a>Distribution av en enda Region
Låt oss börja med en enda region för distribution och inhämta erfarenheter i att skapa en modell för flera regioner. Azure virtuellt nätverk används för att skapa isolerat undernät så att nätverkssäkerhetskrav som nämns ovan kan uppfyllas.  Processen som beskrivs i avsnittet Skapa en enda region distributionen använder Ubuntu 14.04 LTS och Cassandra 2.08. Processen kan dock enkelt antas till andra Linux-varianter. Följande är några av de systemfel egenskaperna för en enda region-distributionen.  

**Hög tillgänglighet:** Cassandra-noderna som visas i bild 1 har distribuerats till två tillgänglighetsuppsättningar så att noderna är fördelade mellan flera feldomäner för hög tillgänglighet. Virtuella datorer med varje tillgänglighetsuppsättning har mappats till 2 feldomäner. Azure använder begreppet feldomän för att hantera oplanerade driftstopp (till exempel maskinvaru- eller fel). Begreppet uppgraderingsdomän (till exempel värd eller gäst-OS-uppdateringar/uppgraderingar, programuppgraderingar) används för att hantera schemalagda driftstopp. Se [Haveriberedskap och hög tillgänglighet för Azure-program](http://msdn.microsoft.com/library/dn251004.aspx) roll i fel- och uppgraderingsdomäner för att uppnå hög tillgänglighet.

![Distribution av en enda region](./media/cassandra-nodejs/cassandra-linux1.png)

Bild 1: Distribution av en enda region

Observera att när detta skrivs Azure tillåter inte att explicit mappningen för en grupp av virtuella datorer till en specifik feldomän; därför även med distributionsmodellen visas i bild 1, är det statistiskt troligt att de virtuella datorerna kan mappas till två feldomäner i stället för fyra.

**Läs in belastningsutjämning Thrift trafik:** Thrift klientbibliotek i webbservern ansluter till klustret via en intern belastningsutjämnare. Detta kräver att processen att lägga till den interna belastningsutjämnaren till undernätet ”data” (se bild 1) i samband med Molntjänsten som är värd för Cassandra-klustret. När den interna belastningsutjämnaren har definierats, kräver varje nod belastningsutjämnade slutpunkten som ska läggas till med kommentarer i en belastningsutjämnad uppsättning med tidigare definierade namnet på belastningsutjämnaren. Se [Azure Internal Load Balancing ](../../../load-balancer/load-balancer-internal-overview.md)för mer information.

**Klustret frö:** är det viktigt att välja de flesta med hög tillgänglighet noderna för frö eftersom de nya noderna kommunicera med startvärdesnoder vill identifiera topologin i klustret. En nod från varje tillgänglighetsuppsättning har utsetts startvärdesnoder att undvika enskild felpunkt.

**Replikeringsfaktor och konsekvensnivå:** Cassandras inbyggd hög tillgänglighet och data hållbarhet kännetecknas av Replikeringsfaktorn (RF - antal kopior av varje rad som lagras i klustret) och konsekvensnivå (antal repliker till vara lästa/skrivna innan resultatet returneras till anroparen.) Replikeringsfaktor anges när du skapar KEYSPACE (liknar en relationsdatabas) medan konsekvensnivån som anges vid utfärdande CRUD-frågan. Dokumentationen om Cassandra på [konfigurera för konsekvens](https://docs.datastax.com/en/cassandra/3.0/cassandra/dml/dmlConfigConsistency.html) konsekvens information och formel för beräkning, kvorum.

Cassandra stöder två typer av integritet datamodeller – konsekvens och slutlig konsekvens; Replikeringsfaktor och konsekvensnivå avgör tillsammans om data är konsekventa så fort en skrivåtgärd är slutförd eller eventuell konsekvens. Till exempel anger KVORUM som Konsekvensnivån alltid säkerställer att data konsekvens vid eventuella konsekvensnivå nedan antalet repliker som ska skrivas som behövs för att uppnå KVORUM (till exempel en) resultatet information är konsekvent.

8 noder klustret som anges ovan, med replikeringsfaktorn 3 och KVORUM (2 noder läsas eller skrivas för att få konsekvens) Läs/Skriv konsekvensnivå, kan överleva teoretisk förlusten av högst 1 nod och replikeringsgrupp innan programmet start märker den fel. Detta förutsätter att alla viktiga blankstegen har även nätverksbelastning Läs/Skriv begäranden.  Följande är de parametrar som används för distribuerade klustret:

Klusterkonfiguration för en enda region Cassandra:

| Kluster-Parameter | Värde | Kommentarer |
| --- | --- | --- |
| Antalet noder (N) |8 |Totalt antal noder i klustret |
| Replikeringsfaktor (RF) |3 |Antal repliker på en viss rad |
| Konsekvensnivå (skriva) |QUORUM[(RF/2) +1) = 2] är resultatet av formeln avrundas nedåt |Skriver högst 2 repliker innan svaret skickas till anroparen; 3-replikering är skriven i ett konsekvent sätt. |
| Konsekvensnivå (läsa) |KVORUM [(RF/2) + 1 = 2] resultatet av formeln avrundas nedåt |Läser 2 repliker innan du skickar svar till anroparen. |
| Replikeringsstrategi |NetworkTopologyStrategy Se [datareplikering](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archDataDistributeAbout.html) i Cassandra-dokumentationen för mer information |Förstår topologi för distribution och placerar replikerna på noder så att alla repliker inte hamnar i samma rack. |
| Snitch |GossipingPropertyFileSnitch Se [växlar](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archSnitchesAbout.html) i Cassandra-dokumentationen för mer information |NetworkTopologyStrategy använder ett koncept för snitch för att förstå topologin. GossipingPropertyFileSnitch ger bättre kontroll i mappar varje nod till datacenter och rack. Klustret använder sedan ett för att sprida denna information. Detta är mycket enklare i dynamiska IP-inställningen i förhållande till PropertyFileSnitch |

**Azure överväganden för Cassandra-klustret:** kapaciteten för Microsoft Azure Virtual Machines använder Azure Blob storage för diskpersistens; Azure Storage sparar tre repliker av varje disk för att ge hög tålighet. Det innebär att varje rad med data som infogats i en Cassandra-tabell lagras redan i tre repliker. Så sköts datakonsekvens redan av även om replikering faktor (RF) är 1. Huvudproblemet med Replikeringsfaktor 1 är att programmet inträffar avbrott, även om en Cassandra-nod misslyckas. Om en nod avser problem (till exempel maskinvara, programvara systemfel) identifieras av Azure-Infrastrukturkontrollanten, Etablerar det en ny nod i stället använder samma lagringsenheter. Etablera en ny nod för att ersätta den gamla servern kan ta några minuter.  På samma sätt för planerat underhållsaktiviteter som ändringar för gäst-OS, Cassandra uppgraderas och ändringar i programmet Azure-Infrastrukturkontrollanten utför löpande uppgraderingar av noderna i klustret.  Även löpande uppgraderingar kan ta bort några noder samtidigt och kan därför klustret uppleva kort driftstopp för några partitioner. Informationen är dock inte förlorade på grund av den inbyggd redundansen i Azure Storage.  

För system som distribueras till Azure som inte kräver hög tillgänglighet (till exempel cirka 99,9 vilket motsvarar 8.76 timmar/år, se [hög tillgänglighet](http://en.wikipedia.org/wiki/High_availability) information) du kör med RF = 1 och konsekvensnivå = en.  För program med hög tillgänglighet, RF = 3 och konsekvensnivå = KVORUM kan tolerera nertid av en av noderna och en av replikerna. RF = 1 i traditionella distributioner (till exempel lokalt) kan inte användas på grund av dataförlust som härrör från problem angående diskfel.   

## <a name="multi-region-deployment"></a>Distribution i flera regioner
Det hjälper till med distribution i flera regioner utan att behöva några externa verktyg Cassandra's data-center-medveten replikering och konsekvens modellen som beskrivs ovan. Detta skiljer sig från traditionella relationsdatabaser där konfigurerad för databasspegling för flera huvudservrar skrivningar kan vara komplexa. Cassandra i en inställning för flera regioner kan hjälpa till med Användningsscenarier, inklusive scenarier:

**Närhet baserat distribution:** program för flera innehavare med tydliga mappning av klientanvändare-till-region, kan vara fått med korta svarstider för kluster för flera regioner. Till exempel en learning hanteringssystem för skolor kan distribuera ett distribuerat kluster i östra USA och västra USA-regioner för att hantera respektive Campus för transaktionell samt analytics. Data kan vara lokalt konsekvent i tid läsningar och skrivningar och kan vara konsekvent mellan de båda regionerna. Det finns andra exempel som Mediedistribution, e-handel och vad som helst och allt som har geo koncentrerade användaren grundläggande är ett bra användningsfall för den här modellen.

**Hög tillgänglighet:** redundans är avgörande för att uppnå hög tillgänglighet för program- och maskinvara; information finns i Skapa tillförlitliga Cloud Systems på Microsoft Azure. På Microsoft Azure är bara tillförlitligt sätt att uppnå sann redundans genom att distribuera ett kluster för flera regioner. Program kan distribueras i en aktiv-aktiv eller aktiv-passiv läge och om en av regionerna är nere kan Azure Traffic Manager kan dirigera trafik till regionen active.  Med en enda region-distribution om tillgängligheten är 99,9, en distribution i två regioner kan uppnå 99,9999 beräknas av formeln tillgänglighet: (1-(1-0.999) * (1-0,999)) * 100); finns i ovan för information.

**Katastrofåterställning:** flera regioner Cassandra-klustret om korrekt utformad klarar att kritiska data center avbrott. Om en region är nere kan starta program som distribueras till andra regioner betjänar slutanvändare. Programmet måste vara feltolerant för vissa dataförluster som härrör från data i asynkrona pipelinen som alla andra business continuity-implementeringar kan. Dock gör Cassandra återställningen mycket snabbare än den tid som återställningsprocesserna för traditionell databas. Bild 2 visar den typiska distribution i flera regioner med åtta noder i varje region. Båda regionerna är speglade bilder från varandra för samma av symmetri; verkliga Designer beror på Arbetsbelastningstyp av (till exempel transaktionsappar eller analysappar), RPO, RTO, datakonsekvens och kraven på tillgänglighet.

![Distribution i flera regioner](./media/cassandra-nodejs/cassandra-linux2.png)

Bild 2: Distribution av flera regioner Cassandra

### <a name="network-integration"></a>Nätverksintegrering
Uppsättningar av virtuella datorer distribueras på privata nätverk som finns på två regioner som kommunicerar med varandra via en VPN-tunnel. VPN-tunneln ansluter två programvara-gateways som tillhandahålls under distributionsprocessen nätverk. Båda regioner har liknande nätverksarkitektur när det gäller ”web” och ”data” undernät; Azure-nätverk kan skapa så många undernät om det behövs och tillämpa ACL: er som krävs för nätverkssäkerhet. När du utformar klustertopologi bland data center kommunikation svarstid och ekonomiska effekten av trafik behovet av nätverk för att anses vara.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Datakonsekvens för distribution av flera Datacenter
Distribuerade distributioner måste vara medveten om klustret topologi påverkan på genomströmning och hög tillgänglighet. RF och konsekvensnivå måste väljas så att kvorum inte beror på tillgängligheten för alla datacenter.
För ett system som behöver hög konsekvens, en LOCAL_QUORUM för konsekvensnivå (för läsning och skrivning) som ser till att lokala läsningar och skrivningar uppfylls från de lokala noderna medan data replikeras asynkront till de fjärranslutna datacenter.  Tabell 2 innehåller en sammanfattning av konfigurationsinformationen för kluster för flera regioner som beskrivs senare i Skriv upp.

**Två regioner Cassandra-klusterkonfiguration**

| Kluster-Parameter | Värde | Kommentarer |
| --- | --- | --- |
| Antalet noder (N) |8 + 8 |Totalt antal noder i klustret |
| Replikeringsfaktor (RF) |3 |Antal repliker på en viss rad |
| Konsekvensnivå (skriva) |LOCAL_QUORUM [(sum(RF)/2) +1) = 4] resultatet av formeln avrundas nedåt |2 noder skrivs till den första datacentralen synkront; ytterligare 2 noder som behövs för att kvorum skrivs asynkront till 2-datacentret. |
| Konsekvensnivå (läsa) |LOCAL_QUORUM ((RF/2) + 1) = 2, resultatet av formeln avrundas nedåt |Läsbegäranden uppfylls från endast en region. 2 noder är skrivskyddade innan svaret skickas tillbaka till klienten. |
| Replikeringsstrategi |NetworkTopologyStrategy Se [datareplikering](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archDataDistributeAbout.html) i Cassandra-dokumentationen för mer information |Förstår topologi för distribution och placerar replikerna på noder så att alla repliker inte hamnar i samma rack. |
| Snitch |GossipingPropertyFileSnitch Se [Snitches](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archSnitchesAbout.html) i Cassandra-dokumentationen för mer information |NetworkTopologyStrategy använder ett koncept för snitch för att förstå topologin. GossipingPropertyFileSnitch ger bättre kontroll i mappar varje nod till datacenter och rack. Klustret använder sedan ett för att sprida denna information. Detta är mycket enklare i dynamiska IP-inställningen i förhållande till PropertyFileSnitch |

## <a name="the-software-configuration"></a>KONFIGURATION AV PROGRAMVARA
Följande programvaruversioner som används under distributionen:

<table>
<tr><th>Programvara</th><th>Källa</th><th>Version</th></tr>
<tr><td>JRE    </td><td>[JRE 8](https://aka.ms/azure-jdks) </td><td>8U5</td></tr>
<tr><td>JNA    </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu    </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Hämta all nödvändig programvara för att förenkla distributionen på skrivbordet. Sedan överföra det till Ubuntu-mallavbildningen för att skapa som ett tidigare kluster-distributionen.

Ladda ned programmen ovan till en välkänd katalog för hämtade filer (till exempel %TEMP%/downloads på Windows eller ~/Downloads på de flesta Linux-distributioner eller Mac) på den lokala datorn.

### <a name="create-ubuntu-vm"></a>SKAPA VIRTUELL UBUNTU-DATOR
I det här steget i processen skapar du Ubuntu-avbildning med programvara som krävs så att du kan återanvända avbildningen för att etablera flera Cassandra-noder.  

#### <a name="step-1-generate-ssh-key-pair"></a>STEG 1: Skapa SSH-nyckelpar
Azure behöver en X509 offentliga nyckel som är PEM eller DER-kodad då etablering. Generera ett offentligt/privat nyckelpar med hjälp av anvisningarna som finns på hur du kan använda SSH med Linux på Azure. Om du planerar att använda putty.exe som en SSH-klient på Windows eller Linux, måste du konvertera den PEM-kodat RSA PPK-format med hjälp av puttygen.exe den privata nyckeln. Instruktioner för detta finns på webbsidan som ovan.

#### <a name="step-2-create-ubuntu-template-vm"></a>STEG 2: Skapa VM-mallen för Ubuntu
Skapa mall för virtuell dator, logga in på Azure portal och Använd följande ordning: Klicka på ny, beräkning, virtuell dator, från GALLERIET, UBUNTU, Ubuntu Server 14.04 LTS och klicka sedan på pilen till höger. En självstudiekurs som beskriver hur du skapar en Linux VM, se Skapa en virtuell dator som kör Linux.

Ange följande information på skärmen ”konfiguration av virtuell dator” #1:

<table>
<tr><th>FÄLTNAMN              </td><td>       FÄLTVÄRDE               </td><td>         ANMÄRKNING                </td><tr>
<tr><td>VERSION LANSERINGSDATUM    </td><td> Välj ett datum i listrutan ned</td><td></td><tr>
<tr><td>NAMN PÅ VIRTUELL DATOR    </td><td> CAS-mall                   </td><td> Det här är värdnamnet för den virtuella datorn </td><tr>
<tr><td>NIVÅ                     </td><td> STANDARD                           </td><td> Låt standardvärdet              </td><tr>
<tr><td>STORLEK                     </td><td> A1                              </td><td>Välj den virtuella datorn baserat på i/o-behov. Låt standardvärdet för detta ändamål </td><tr>
<tr><td> NYTT ANVÄNDARNAMN             </td><td> localadmin                       </td><td> ”admin” är ett reserverat användarnamn i Ubuntu 12. xx och när</td><tr>
<tr><td> AUTENTISERING         </td><td> Klicka på kryssrutan                 </td><td>Markera om du vill skydda med en SSH-nyckel </td><tr>
<tr><td> CERTIFIKAT             </td><td> filnamnet på det offentliga nyckelcertifikatet </td><td> Använd den offentliga nyckeln som skapats tidigare</td><tr>
<tr><td> Nytt lösenord    </td><td> starkt lösenord </td><td> </td><tr>
<tr><td> Bekräfta lösenord    </td><td> starkt lösenord </td><td></td><tr>
</table>

Ange följande information på skärmen ”konfiguration av virtuell dator” #2:

<table>
<tr><th>FÄLTNAMN             </th><th> FÄLTVÄRDE                       </th><th> ANMÄRKNING                                 </th></tr>
<tr><td> MOLNTJÄNST    </td><td> Skapa en ny molntjänst    </td><td>Molntjänst är en behållare beräkningsresurser som virtuella datorer</td></tr>
<tr><td> MOLNTJÄNSTENS DNS-NAMN    </td><td>ubuntu-template.cloudapp.net    </td><td>Ge ett namn för datorn oberoende load balancer</td></tr>
<tr><td> REGION/TILLHÖRIGHETSGRUPP/VIRTUELLT NÄTVERK </td><td>    Västra USA    </td><td> Välj en region där dina webbprogram åtkomst till Cassandra-klustret</td></tr>
<tr><td>LAGRINGSKONTO </td><td>    Använd standard    </td><td>Använd standardkontot för lagring eller ett lagringskonto som skapats i förväg i en viss region</td></tr>
<tr><td>TILLGÄNGLIGHETSUPPSÄTTNING </td><td>    Ingen </td><td>    Lämna det tomt</td></tr>
<tr><td>Slutpunkter    </td><td>Använd standard </td><td>    Använd standardkonfigurationen för SSH </td></tr>
</table>

Klicka på högerpilen, lämna standardvärdena på skärmen #3. Klicka på knappen ”Kontrollera” för att slutföra etableringen för virtuell dator. Efter några minuter, måste den virtuella datorn med namnet ”ubuntu-template” vara i statusen ”körs”.

### <a name="install-the-necessary-software"></a>INSTALLERA NÖDVÄNDIG PROGRAMVARA
#### <a name="step-1-upload-tarballs"></a>STEG 1: Ladda upp tarballs
Med hjälp av scp eller pscp, kopiera tidigare hämtade programvaran till ~/downloads katalog med hjälp av följande kommandoformatet:

##### <a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp server-jre-8u5 – linux-x64.tar.gz localadmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz
Upprepa kommandot ovan för JRE samt för Cassandra-bitar.

#### <a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>STEG 2: Förbereda katalogstrukturen och extrahera arkivet
Logga in på den virtuella datorn och skapa katalogstrukturen och extrahera programvara som en superanvändare som med bash-skript nedan:

    #!/bin/bash
    CASS_INSTALL_DIR="/opt/cassandra"
    JRE_INSTALL_DIR="/opt/java"
    CASS_DATA_DIR="/var/lib/cassandra"
    CASS_LOG_DIR="/var/log/cassandra"
    DOWNLOADS_DIR="~/downloads"
    JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
    CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
    SVC_USER="localadmin"

    RESET_ERROR=1
    MKDIR_ERROR=2

    reset_installation ()
    {
       rm -rf $CASS_INSTALL_DIR 2> /dev/null
       rm -rf $JRE_INSTALL_DIR 2> /dev/null
       rm -rf $CASS_DATA_DIR 2> /dev/null
       rm -rf $CASS_LOG_DIR 2> /dev/null
    }
    make_dir ()
    {
       if [ -z "$1" ]
       then
          echo "make_dir: invalid directory name"
          exit $MKDIR_ERROR
       fi

       if [ -d "$1" ]
       then
          echo "make_dir: directory already exists"
          exit $MKDIR_ERROR
       fi

       mkdir $1 2>/dev/null
       if [ $? != 0 ]
       then
          echo "directory creation failed"
          exit $MKDIR_ERROR
       fi
    }

    unzip()
    {
       if [ $# == 2 ]
       then
          tar xzf $1 -C $2
       else
          echo "archive error"
       fi

    }

    if [ -n "$1" ]
    then
       SVC_USER=$1
    fi

    reset_installation
    make_dir $CASS_INSTALL_DIR
    make_dir $JRE_INSTALL_DIR
    make_dir $CASS_DATA_DIR
    make_dir $CASS_LOG_DIR

    #unzip JRE and Cassandra
    unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
    unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR

    #Change the ownership to the service credentials

    chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
    chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
    echo "edit /etc/profile to add JRE to the PATH"
    echo "installation is complete"


Om du klistrar in det här skriptet till vim-fönstret, se till att ta bort vagnretur (”\r”) med följande kommando:

    tr -d '\r' <infile.sh >outfile.sh

#### <a name="step-3-edit-etcprofile"></a>Steg 3: Redigera etc/profil
Lägg till följande i slutet:

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

#### <a name="step-4-install-jna-for-production-systems"></a>Steg 4: Installera JNA för produktionssystem
Använd följande kommandosekvens: följande kommando installerar jna 3.2.7.jar och jna plattform 3.2.7.jar till /usr/share.java directory sudo apt-get installera libjna – java

Skapa symboliska länkar i $CASS_HOME/lib directory så att Cassandra startskript kan hitta de här JAR-filer:

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

#### <a name="step-5-configure-cassandrayaml"></a>Steg 5: Konfigurera cassandra.yaml
Redigera cassandra.yaml på varje virtuell dator konfiguration som krävs av alla virtuella datorer [du justera den här konfigurationen under faktiska etableringen]:

<table>
<tr><th>Fältnamn   </th><th> Värde  </th><th>    Kommentarer </th></tr>
<tr><td>klusternamn </td><td>    ”CustomerService”    </td><td> Använd det namn som återspeglar din distribution</td></tr>
<tr><td>listen_address    </td><td>[lämna det tomt]    </td><td> Ta bort ”localhost” </td></tr>
<tr><td>rpc_addres   </td><td>[lämna det tomt]    </td><td> Ta bort ”localhost” </td></tr>
<tr><td>frö    </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"    </td><td>Lista över alla IP-adresser som är tilldelade som frö.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Den används av NetworkTopologyStrateg för härledning av datacentret och rack för den virtuella datorn</td></tr>
</table>

#### <a name="step-6-capture-the-vm-image"></a>Steg 6: Skapa VM-avbildning
Logga in på den virtuella datorn med värdnamn (hk-cas-template.cloudapp.net) och den privata SSH-nyckeln som skapade tidigare. Se hur du använder du SSH med Linux på Azure för information om hur du loggar in med hjälp av kommandot ssh eller putty.exe.

Kör följande sekvens med åtgärder för att avbilda avbildningen:

##### <a name="1-deprovision"></a>1. Avetablering
Använd kommandot ”sudo waagent – avetablering + användare” ta bort VM-instans specifik information. Se för [så här avbildar du en Linux-dator](capture-image-classic.md) ska användas som en mall för mer information på hämtningen av avbildningen.

##### <a name="2-shut-down-the-vm"></a>2: Stäng av den virtuella datorn
Kontrollera att den virtuella datorn har markerats och klicka på länken avstängning i kommandofältet längst ned.

##### <a name="3-capture-the-image"></a>3: samla in en avbildning
Kontrollera att den virtuella datorn har markerats och klicka på länken avbildning från kommandofältet längst ned. I nästa skärm, ge en AVBILDNINGENS namn (till exempel hk-cas-2-08-ub-14-04-2014071), lämplig BILDBESKRIVNING och klickar på Markera ”kontroll” om du vill slutföra bildtagningen.

Den här processen tar några sekunder och avbildningen ska vara tillgänglig under Mina AVBILDNINGAR i galleriet. Den Virtuella källdatorn tas bort automatiskt när avbildningen har avbildats. 

## <a name="single-region-deployment-process"></a>Processen för distribution av en enda Region
**Steg 1: Skapa det virtuella nätverket** logga in på Azure-portalen och skapa ett virtuellt nätverk (klassiskt) med attribut som visas i följande tabell. Se [skapa ett virtuellt nätverk (klassisk) med Azure portal](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för detaljerade steg i processen.      

<table>
<tr><th>Attributnamnet för virtuell dator</th><th>Värde</th><th>Kommentarer</th></tr>
<tr><td>Namn</td><td>vnet-cass-west-us</td><td></td></tr>
<tr><td>Region</td><td>Västra USA</td><td></td></tr>
<tr><td>DNS-servrar</td><td>Ingen</td><td>Ignorera detta eftersom vi inte använder en DNS-Server</td></tr>
<tr><td>Adressutrymme</td><td>10.1.0.0/16</td><td></td></tr>    
<tr><td>Start-IP</td><td>10.1.0.0</td><td></td></tr>    
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

Lägg till följande undernät:

<table>
<tr><th>Namn</th><th>Start-IP</th><th>CIDR</th><th>Kommentarer</th></tr>
<tr><td>webb</td><td>10.1.1.0</td><td>/24 (251)</td><td>Undernät för webbservergruppen</td></tr>
<tr><td>data</td><td>10.1.2.0</td><td>/24 (251)</td><td>Undernät för databasnoderna</td></tr>
</table>

Data och Web undernät kan skyddas genom nätverkssäkerhetsgrupper täckning som ligger utanför omfånget för den här artikeln.  

**Steg 2: Etablera virtuella datorer** med den avbildning som skapades tidigare kan du skapa följande virtuella datorer i cloud server ”hk – c-svc-, västra” och binda dem till respektive undernät enligt nedan:

<table>
<tr><th>Datornamn    </th><th>Undernät    </th><th>IP-adress    </th><th>Tillgänglighetsuppsättning</th><th>DC/Rack</th><th>Dirigera?</th></tr>
<tr><td>hk-c1-west-us    </td><td>data    </td><td>10.1.2.4    </td><td>hk-c-aset-1    </td><td>DC = WESTUS rack = rack1 </td><td>Ja</td></tr>
<tr><td>hk-c2-west-us    </td><td>data    </td><td>10.1.2.5    </td><td>hk-c-aset-1    </td><td>DC = WESTUS rack = rack1    </td><td>Nej </td></tr>
<tr><td>hk-c3-west-us    </td><td>data    </td><td>10.1.2.6    </td><td>hk-c-aset-1    </td><td>DC = WESTUS rack = rack2    </td><td>Ja</td></tr>
<tr><td>hk-c4-west-us    </td><td>data    </td><td>10.1.2.7    </td><td>hk-c-aset-1    </td><td>DC = WESTUS rack = rack2    </td><td>Nej </td></tr>
<tr><td>hk-c5-west-us    </td><td>data    </td><td>10.1.2.8    </td><td>hk-c-aset-2    </td><td>DC = WESTUS rack = rack3    </td><td>Ja</td></tr>
<tr><td>hk-c6-west-us    </td><td>data    </td><td>10.1.2.9    </td><td>hk-c-aset-2    </td><td>DC = WESTUS rack = rack3    </td><td>Nej </td></tr>
<tr><td>hk-c7-west-us    </td><td>data    </td><td>10.1.2.10    </td><td>hk-c-aset-2    </td><td>DC = WESTUS rack = rack4    </td><td>Ja</td></tr>
<tr><td>hk-c8-west-us    </td><td>data    </td><td>10.1.2.11    </td><td>hk-c-aset-2    </td><td>DC = WESTUS rack = rack4    </td><td>Nej </td></tr>
<tr><td>hk-w1-west-us    </td><td>webb    </td><td>10.1.1.4    </td><td>hk-w-aset-1    </td><td>                       </td><td>Gäller inte</td></tr>
<tr><td>hk-w2-west-us    </td><td>webb    </td><td>10.1.1.5    </td><td>hk-w-aset-1    </td><td>                       </td><td>Gäller inte</td></tr>
</table>

Skapa listan ovan för virtuella datorer kräver följande process:

1. Skapa en tom cloud Services i en viss region
2. Skapa en virtuell dator från den tidigare avbildningen och koppla den till det virtuella nätverket som skapades tidigare; Upprepa detta för alla virtuella datorer
3. Lägg till en intern belastningsutjämnare i Molntjänsten och koppla den till undernätet ”data”
4. För varje virtuell dator som skapats tidigare, lägger du till en belastningsutjämnad slutpunkt för thrift-trafik via en belastningsutjämnad uppsättning som är anslutna till den tidigare skapade interna belastningsutjämnaren

Ovanstående procedur kan utföras med hjälp av Azure-portalen; Använd Windows-dator (Använd en virtuell dator i Azure om du inte har åtkomst till en Windows-dator), använda följande PowerShell-skript för att etablera alla 8 virtuella datorer automatiskt.

**Lista 1: PowerShell-skript för etablering av virtuella datorer**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. create a list of VMs from the template

        #fundamental variables - change these to reflect your subscription
        $country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
        $numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
        $subscriptionName = "Azure_subscription_name";
        $vmSize="ExtraSmall"; $imageName="your_linux_image_name"
        $ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"

        #generated variables
        $serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"

        $vmNames = @()
        for ($i=0; $i -lt $numVMs; $i++)
        {
           $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
        }

        #select an Azure subscription already imported into Powershell session
        Select-AzureSubscription -SubscriptionName $subscriptionName -Current
        Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount

        #create an empty cloud service
        New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
        Write-Host "Created $serviceName"

        $VMList= @()   # stores the list of azure vm configuration objects
        #create the list of VMs
        foreach($vmName in $vmNames)
        {
           $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
           Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
           Set-AzureSubnet "data"
        }

        New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList

        #Create internal load balancer
        Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
        Write-Host "Created $ilbName"
        #Add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**Steg 3: Konfigurera Cassandra på varje virtuell dator**

Logga in på den virtuella datorn och gör följande:

* Redigera $CASS_HOME/conf/cassandra-rackdc.properties för att ange egenskaper för data center och rack:
  
       dc =EASTUS, rack =rack1
* Redigera cassandra.yaml om du vill konfigurera startvärdesnoder enligt nedan:
  
       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Steg 4: Starta de virtuella datorerna och testar klustret**

Logga in på en av noderna (till exempel hk-c1 – USA, västra –) och kör följande kommando för att se status för klustret:

       nodetool –h 10.1.2.4 –p 7199 status

Du bör se det som visningen liknar den nedan för ett kluster på 8 noder:

<table>
<tr><th>Status</th><th>Adress    </th><th>Läsa in    </th><th>Token    </th><th>Äger </th><th>Värd-ID    </th><th>Rack</th></tr>
<tr><th>TA BORT    </td><td>10.1.2.4     </td><td>87.81 KB    </td><td>256    </td><td>38.0%    </td><td>GUID (tas bort)</td><td>rack1</td></tr>
<tr><th>TA BORT    </td><td>10.1.2.5     </td><td>41.08 KB    </td><td>256    </td><td>68.9%    </td><td>GUID (tas bort)</td><td>rack1</td></tr>
<tr><th>TA BORT    </td><td>10.1.2.6     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (tas bort)</td><td>rack2</td></tr>
<tr><th>TA BORT    </td><td>10.1.2.7     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (tas bort)</td><td>rack2</td></tr>
<tr><th>TA BORT    </td><td>10.1.2.8     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (tas bort)</td><td>rack3</td></tr>
<tr><th>TA BORT    </td><td>10.1.2.9     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (tas bort)</td><td>rack3</td></tr>
<tr><th>TA BORT    </td><td>10.1.2.10     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (tas bort)</td><td>rack4</td></tr>
<tr><th>TA BORT    </td><td>10.1.2.11     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (tas bort)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Testa en enda Region-kluster
Använd följande steg för att testa klustret:

1. Med hjälp av Powershell-kommandot Get-AzureInternalLoadbalancer-kommandot, skaffa IP-adressen för den interna belastningsutjämnaren (till exempel 10.1.2.101). Syntaxen för kommandot visas nedan: Get-AzureLoadbalancer – ServiceName ”hk-c-svc-west-us” [visar information om den interna belastningsutjämnaren tillsammans med dess IP-adress]
2. Logga in på webbservergrupp VM (till exempel hk-w1 – USA, västra –) med hjälp av Putty eller ssh
3. Köra $CASS_HOME/bin/cqlsh 10.1.2.101 9160
4. Använd följande CQL-kommandon för att kontrollera om klustret fungerar:
   
     Skapa KEYSPACE customers_ks med replikering = {'class': 'SimpleStrategy', 'replication_factor': 3};   ANVÄNDA customers_ks;   Skapa tabell Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Infoga i Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, ”Jane”, ”Doe”).
   
     Välj * från kunder;

Du bör se något som liknar följande resultat:

<table>
  <tr><th> customer_id </th><th> Förnamn </th><th> Efternamn </th></tr>
  <tr><td> 1 </td><td> John </td><td> Berg </td></tr>
  <tr><td> 2 </td><td> Jane </td><td> Berg </td></tr>
</table>

Keyspace skapade i steg 4 använder SimpleStrategy med en replication_factor 3. SimpleStrategy rekommenderas för enkel data center distributioner medan NetworkTopologyStrategy för flera data datacentraler. En replication_factor 3 ger feltolerans för nodfel.

## <a id="tworegion"> </a>Processen för distribution i flera regioner
Du använder en enda region-distributionen har slutförts och upprepa samma steg för att installera den andra regionen. Den stora skillnaden mellan distribution för en eller flera regioner har konfigurerats för VPN-tunnel för kommunikation mellan regioner. du börjar med nätverksinstallation, etablera de virtuella datorerna och konfigurera Cassandra.

### <a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>Steg 1: Skapa det virtuella nätverket i regionen 2
Logga in på Azure-portalen och skapa ett virtuellt nätverk med attribut som visas i tabellen. Se [konfigurera ett virtuellt nätverk för Cloud-Only i Azure-portalen](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för detaljerade steg i processen.      

<table>
<tr><th>Attributnamn    </th><th>Värde    </th><th>Kommentarer</th></tr>
<tr><td>Namn    </td><td>vnet-cass-east-us</td><td></td></tr>
<tr><td>Region    </td><td>Östra USA</td><td></td></tr>
<tr><td>DNS-servrar        </td><td></td><td>Ignorera detta eftersom vi inte använder en DNS-Server</td></tr>
<tr><td>Konfigurera en punkt-till-plats-VPN</td><td></td><td>        Ignorera detta</td></tr>
<tr><td>Konfigurera ett VPN mellan två platser</td><td></td><td>        Ignorera detta</td></tr>
<tr><td>Adressutrymme    </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>Start-IP    </td><td>10.2.0.0    </td><td></td></tr>
<tr><td>CIDR    </td><td>/16 (65531)</td><td></td></tr>
</table>

Lägg till följande undernät:

<table>
<tr><th>Namn    </th><th>Start-IP    </th><th>CIDR    </th><th>Kommentarer</th></tr>
<tr><td>webb    </td><td>10.2.1.0    </td><td>/24 (251)    </td><td>Undernät för webbservergruppen</td></tr>
<tr><td>data    </td><td>10.2.2.0    </td><td>/24 (251)    </td><td>Undernät för databasnoderna</td></tr>
</table>


### <a name="step-2-create-local-networks"></a>Steg 2: Skapa lokala nätverk
Ett lokalt nätverk i Azure virtuella nätverk är en proxy-adressutrymme som mappar till en fjärrplats, inklusive ett privat moln eller en annan Azure-region. Det här adressutrymmet för proxy är bunden till en fjärr-gateway för routning nätverket till rätt nätverk plats. Se [konfigurera ett VNet till VNet-anslutning](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) anvisningar för VNET-till-VNET-anslutning.

Skapa två lokala nätverk per följande information:

| Nätverksnamn | VPN Gateway-adress | Adressutrymme | Kommentarer |
| --- | --- | --- | --- |
| hk-lnet-map-to-east-us |23.1.1.1 |10.2.0.0/16 |Ge en platshållare gatewayadress när du skapar det lokala nätverket. Den verkliga Gatewayadressen fylls när gatewayen har skapats. Kontrollera att adressutrymmet exakt matchar den respektive Virtuella fjärrnätverket; det virtuella nätverket skapas i det här fallet i regionen östra USA. |
| hk-lnet-map-to-west-us |23.2.2.2 |10.1.0.0/16 |Ge en platshållare gatewayadress när du skapar det lokala nätverket. Den verkliga Gatewayadressen fylls när gatewayen har skapats. Kontrollera att adressutrymmet exakt matchar den respektive Virtuella fjärrnätverket; i det här fallet det virtuella nätverket som har skapats i regionen USA, västra. |

### <a name="step-3-map-local-network-to-the-respective-vnets"></a>Steg 3: Kartan ”Local” nätverk till respektive virtuella nätverk
Välj varje virtuellt nätverk från Azure-portalen, klickar du på ”Konfigurera”, markera ”Anslut till det lokala nätverket” och välj de lokala nätverken per följande information:

| Virtual Network | Lokalt nätverk |
| --- | --- |
| hk-vnet-west-us |hk-lnet-map-to-east-us |
| hk-vnet-east-us |hk-lnet-map-to-west-us |

### <a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>Steg 4: Skapa gatewayer i VNET1 och VNET2
Klicka på Skapa GATEWAY för att utlösa en VPN-gateway etableringsprocessen från instrumentpanelen för båda de virtuella nätverken. Instrumentpanelen för varje virtuellt nätverk ska visa den faktiska Gatewayadressen efter ett par minuter.

### <a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>Steg 5: Uppdatera ”Local” nätverk med respektive ”Gateway”-adresser
Redigera både de lokala nätverk om du vill ersätta platshållaren gatewayens IP-adress med verkliga IP-adressen bara etablerade gateway. Använd följande mappning:

<table>
<tr><th>Lokalt nätverk    </th><th>Virtuell nätverksgateway</th></tr>
<tr><td>hk-lnet-map-to-east-us </td><td>Gateway för hk – virtuellt nätverk – USA, västra –</td></tr>
<tr><td>hk-lnet-map-to-west-us </td><td>Gateway för hk-vnet-Öst-USA</td></tr>
</table>

### <a name="step-6-update-the-shared-key"></a>Steg 6: Uppdatera den delade nyckeln
Använd följande Powershell-skript för att uppdatera IPSec-nyckeln för varje VPN gateway [används den tänker några viktiga nyckeln för båda gatewayerna]: Set-AzureVNetGatewayKey - VNetName hk-vnet-Öst-oss - LocalNetworkSiteName hk-lnet-map-to-west-us - SharedKey D9E76BKK Set-AzureVNetGatewayKey - VNetName hk – virtuellt nätverk – USA, västra – - LocalNetworkSiteName hk-lnet-map-to-east-us - SharedKey D9E76BKK

### <a name="step-7-establish-the-vnet-to-vnet-connection"></a>Steg 7: Upprätta VNET-till-VNET-anslutning
Använd ”INSTRUMENTPANEL”-menyn i båda de virtuella nätverken från Azure-portalen för att upprätta anslutning för gateway-till-gateway. Använd menyalternativ ”Anslut” i verktygsfältet längst ned. Efter ett par minuter ska instrumentpanelen visa anslutningsinformationen grafiskt.

### <a name="step-8-create-the-virtual-machines-in-region-2"></a>Steg 8: Skapa de virtuella datorerna i regionen #2
Skapa Ubuntu-avbildningen enligt beskrivningen i regionen #1 distribution genom att följa samma steg eller kopiera avbildningen VHD-filen till Azure storage-kontot finns i området #2 och skapa avbildningen. Använd den här avbildningen och skapa följande lista över virtuella datorer till en ny molntjänst hk-c-svc-Öst-oss:

| Datornamn | Undernät | IP-adress | Tillgänglighetsuppsättning | DC/Rack | Dirigera? |
| --- | --- | --- | --- | --- | --- |
| HK – c1-Öst-USA |data |10.2.2.4 |hk-c-aset-1 |DC = EASTUS rack = rack1 |Ja |
| hk-c2-east-us |data |10.2.2.5 |hk-c-aset-1 |DC = EASTUS rack = rack1 |Nej |
| hk-c3-east-us |data |10.2.2.6 |hk-c-aset-1 |DC = EASTUS rack = rack2 |Ja |
| hk-c5-east-us |data |10.2.2.8 |hk-c-aset-2 |DC = EASTUS rack = rack3 |Ja |
| HK – c6-Öst-USA |data |10.2.2.9 |hk-c-aset-2 |DC = EASTUS rack = rack3 |Nej |
| HK – c7-Öst-USA |data |10.2.2.10 |hk-c-aset-2 |DC = EASTUS rack = rack4 |Ja |
| HK – c8-Öst-USA |data |10.2.2.11 |hk-c-aset-2 |DC = EASTUS rack = rack4 |Nej |
| HK-w1-Öst-USA |webb |10.2.1.4 |hk-w-aset-1 |Gäller inte |Gäller inte |
| HK – w2-Öst-USA |webb |10.2.1.5 |hk-w-aset-1 |Gäller inte |Gäller inte |

Följ samma anvisningar som region #1 men använder 10.2.xxx.xxx adressutrymme.

### <a name="step-9-configure-cassandra-on-each-vm"></a>Steg 9: Konfigurera Cassandra på varje virtuell dator
Logga in på den virtuella datorn och gör följande:

1. Redigera $CASS_HOME/conf/cassandra-rackdc.properties för att ange egenskaper för data center och rack i formatet: dc = EASTUS rack = rack1
2. Redigera cassandra.yaml om du vill konfigurera startvärdesnoder: frö: ”10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10”

### <a name="step-10-start-cassandra"></a>Steg 10: Starta Cassandra
Logga in på varje virtuell dator och starta Cassandra i bakgrunden genom att köra följande kommando: $CASS_HOME/bin/cassandra

## <a name="test-the-multi-region-cluster"></a>Testa kluster för flera regioner
Nu har Cassandra distribuerats till 16 noder med 8 noder i varje Azure-region. Dessa noder finns i samma kluster tack vare vanliga klusternamnet och nodkonfiguration startvärde. Du kan använda följande process för att testa klustret:

### <a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>Steg 1: Hämta interna belastningsutjämnarens IP-Adressen för båda regionerna med hjälp av PowerShell
* Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-west-us"
* Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-east-us"  
  
    Observera de IP-adresserna (för exempel Väst - 10.1.2.101, östra – 10.2.2.101) visas.

### <a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>Steg 2: Kör följande i region Väst när du loggar in hk-w1 – USA, västra –
1. Köra $CASS_HOME/bin/cqlsh 10.1.2.101 9160
2. Kör följande kommandon för CQL:
   
     Skapa KEYSPACE customers_ks med replikering = {'class': 'NetworkToplogyStrategy', 'WESTUS': 3, EASTUS: 3};   ANVÄNDA customers_ks;   Skapa tabell Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Infoga i Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, ”Jane”, ”Doe”).   Välj * från kunder;

Du bör se en skärm som liknar den nedan:

| customer_id | Förnamn | Efternamn |
| --- | --- | --- |
| 1 |John |Berg |
| 2 |Jane |Berg |

### <a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>Steg 3: Kör följande i region Öst när du loggar in hk-w1-Öst-oss:
1. Köra $CASS_HOME/bin/cqlsh 10.2.2.101 9160
2. Kör följande kommandon för CQL:
   
     ANVÄNDA customers_ks;   Skapa tabell Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Infoga i Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, ”Jane”, ”Doe”).   Välj * från kunder;

Du bör se samma skärm som visas för regionen West:

| customer_id | Förnamn | Efternamn |
| --- | --- | --- |
| 1 |John |Berg |
| 2 |Jane |Berg |

Köra några fler infogningar och se att de replikeras till Väst-oss en del av klustret.

## <a name="test-cassandra-cluster-from-nodejs"></a>Cassandra Testkluster från Node.js
Använder en av de Linux virtuella datorer som skapades i ”webb”-nivån kan köra du ett enkelt Node.js-skript för att läsa den tidigare infogade data

**Steg 1: Installera Node.js och Cassandra-klienten**

1. Installera Node.js och npm
2. Installera noden paketet ”cassandra-klient” med npm
3. Kör följande skript i shell-prompten som visar json-sträng för hämtade data:
   
        var pooledCon = require('cassandra-client').PooledConnection;
        var ksName = "custsupport_ks";
        var cfName = "customers_cf";
        var hostList = ['internal_loadbalancer_ip:9160'];
        var ksConOptions = { hosts: hostList,
                             keyspace: ksName, use_bigints: false };
   
        function createKeyspace(callback){
           var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
           var sysConOptions = { hosts: hostList,  
                                 keyspace: 'system', use_bigints: false };
           var con = new pooledCon(sysConOptions);
           con.execute(cql,[],function(err) {
           if (err) {
             console.log("Failed to create Keyspace: " + ksName);
             console.log(err);
           }
           else {
             console.log("Created Keyspace: " + ksName);
             callback(ksConOptions, populateCustomerData);
           }
           });
           con.shutdown();
        }
   
        function createColumnFamily(ksConOptions, callback){
          var params = ['customers_cf','custid','varint','custname',
                        'text','custaddress','text'];
          var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
        var con =  new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) {
                 console.log("Failed to create column family: " + params[0]);
                 console.log(err);
              }
              else {
                 console.log("Created column family: " + params[0]);
                 callback();
              }
          });
          con.shutdown();
        }
   
        //populate Data
        function populateCustomerData() {
           var params = ['John','Infinity Dr, TX', 1];
           updateCustomer(ksConOptions,params);
   
           params = ['Tom','Fermat Ln, WA', 2];
           updateCustomer(ksConOptions,params);
        }
   
        //update also inserts the record if none exists
        function updateCustomer(ksConOptions,params)
        {
          var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) console.log(err);
              else console.log("Inserted customer : " + params[0]);
          });
          con.shutdown();
        }
   
        //read the two rows inserted above
        function readCustomer(ksConOptions)
        {
          var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,[],function(err,rows) {
              if (err)
                 console.log(err);
              else
                 for (var i=0; i<rows.length; i++)
                    console.log(JSON.stringify(rows[i]));
            });
           con.shutdown();
        }
   
        //exectue the code
        createKeyspace(createColumnFamily);
        readCustomer(ksConOptions)

## <a name="conclusion"></a>Sammanfattning
Microsoft Azure är en flexibel plattform som tillåter körning av både Microsoft samt programvara med öppen källkod som visas av den här övningen. Cassandra-kluster med hög tillgänglighet kan distribueras på ett datacenter genom att klusternoderna sprids över flera feldomäner. Cassandra-kluster kan också distribueras över flera geografiskt avlägsna Azure-regioner för katastrofåterställning bevis system. Azure och Cassandra kan tillsammans konstruktion av mycket skalbar, med hög tillgänglighet och katastrofåterställning återställningsbara molntjänster behövs av dagens internet skala tjänster.  

## <a name="references"></a>Referenser
* [http://cassandra.apache.org](http://cassandra.apache.org)
* [http://www.datastax.com](http://www.datastax.com)
* [http://www.nodejs.org](http://www.nodejs.org)

