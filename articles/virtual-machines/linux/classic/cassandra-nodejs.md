---
title: "Kör en Cassandra kluster på Linux i Azure från Node.js"
description: "Hur du kör ett Cassandra kluster på Linux i Azure Virtual Machines från en Node.js-app"
services: virtual-machines-linux
documentationcenter: nodejs
author: craigshoemaker
manager: routlaw
editor: 
tags: azure-service-management
ms.assetid: 30de1f29-e97d-492f-ae34-41ec83488de0
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: cshoe
ms.openlocfilehash: 3d552ae8593773fbf17cd19344f1ddb4d3a49fba
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="run-a-cassandra-cluster-on-linux-in-azure-with-nodejs"></a>Kör en Cassandra kluster på Linux i Azure med Node.js

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Se Resource Manager-mallar för [Datastax Enterprise](https://azure.microsoft.com/documentation/templates/datastax) och [Väck klustret och Cassandra på CentOS](https://azure.microsoft.com/documentation/templates/spark-and-cassandra-on-centos/).

## <a name="overview"></a>Översikt
Microsoft Azure är en öppen molnplattform som körs både Microsoft som bra som icke-Microsoft-programvara som innehåller operativsystem, programservrar, meddelanden mellanprogram såväl som SQL- och NoSQL-databaser från båda modellerna kommersiella och Öppna källa. Bygga flexibel tjänster på offentliga moln, inklusive Azure kräver noggrann planering och arkitektur för avsiktlig för båda programservrarna som korrekt lagring lager. Cassandras distribuerade lagringsarkitektur naturligt hjälper till att skapa hög tillgänglighet system som är feltolerant för klustret. Cassandra är molnskala NoSQL-databas som underhålls av Apache Software Foundation på cassandra.apache.org; Cassandra är skriven i Java och därför körs på både på Windows och Linux plattformar.

Den här artikeln fokuserar på att visa Cassandra distribution på Ubuntu som ett enstaka och flera Datacenter-kluster som utnyttjar Microsoft Azure virtuella datorer och virtuella nätverk. Kluster-distributionen för produktionsarbetsbelastningar optimerade ligger utanför omfånget för den här artikeln eftersom den kräver flera disk nodkonfiguration, lämpliga ring topologi utformning och datamodeller för att stödja nödvändiga replikering, datakonsekvens, genomflöde och hög tillgänglighet.

Den här artikeln tar en grundläggande metod för att visa vad ingår i att skapa klustret Cassandra jämfört med Docker, Chef eller Puppet, vilket kan göra distribution av infrastruktur som är mycket enklare.  

## <a name="the-deployment-models"></a>Distributionsmodellerna
Microsoft Azure-nätverk kan distributionen av isolerat privat kluster åtkomst som kan vara begränsad till uppnå detaljerade nätverkssäkerhet.  Eftersom den här artikeln om hur du visar Cassandra distribution på en grundläggande nivå kan fokuserar vi inte på nivån konsekvens och optimala lagringsdesignen för genomströmning. Här är listan över nätverkskraven för vår hypotetiska klustret:

* Externa system kan inte komma åt Cassandra databasen från inom eller utanför Azure
* Cassandra klustret måste vara bakom en belastningsutjämnare för thrift-trafik
* Distribuera Cassandra noder i två grupper i varje Datacenter för en förbättrad klustret
* Låsa klustret så som endast programmet servergruppen har åtkomst till databasen direkt
* Inga offentliga nätverk slutpunkter än SSH
* Varje Cassandra nod måste en fast interna IP-adress

Cassandra kan distribueras till en enda Azure region eller till flera regioner baserat på distribuerade uppbyggnad arbetsbelastningen. Distributionsmodell för flera regioner kan utnyttjas för att hantera användare närmare till en viss geografisk plats via samma Cassandra infrastruktur. Cassandras inbyggda nod replikering tar hand om synkronisering av flera master skriver härstammar från flera datacenter och anger en konsekvent bild av data till program. Distribution av flera regioner kan också med riskhanteringen av de bredare avbrott i Azure-tjänsten. Cassandras justerbara konsekvens och replikeringstopologin hjälper att uppfylla olika Återställningspunktmål behov av program.

### <a name="single-region-deployment"></a>Distribution av enskild Region
Vi börjar med en enskild region-distribution och inhämta learnings att skapa en modell för flera regioner. Azure virtuella nätverk används för att skapa isolerade undernät så att nätverkssäkerhetskrav som nämns ovan kan uppfyllas.  Processen som beskrivs i samma region distributionen skapas använder Ubuntu 14.04 LTS och Cassandra 2.08; processen kan enkelt antas till andra varianter av Linux. Följande är några av de systemfel egenskaperna för distribution av enskild region.  

**Hög tillgänglighet:** Cassandra noderna visas i bild 1 har distribuerats till två tillgänglighetsuppsättningar så att noderna sprids mellan flera feldomäner för hög tillgänglighet. Virtuella datorer med varje tillgänglighetsuppsättning är mappad till 2 feldomäner.  Microsoft Azure använder begreppet feldomän att hantera oplanerad ned samtidigt (t.ex. maskinvara eller programvara misslyckanden) begreppet uppgraderingsdomänen (t.ex. värd eller OS-korrigering/uppgraderingar, programuppgraderingar) används för att hantera schemalagda stillestånd. Se [katastrofåterställning och hög tillgänglighet för Azure-program](http://msdn.microsoft.com/library/dn251004.aspx) för fel- och domäner för att uppnå hög tillgänglighet.

![Distribution av enskild region](./media/cassandra-nodejs/cassandra-linux1.png)

Figur 1: Distribution av enskild region

Observera att när detta skrivs Azure tillåter inte att explicit mappningen för en grupp med virtuella datorer till en specifik feldomän; även om den distributionsmodell som illustreras i bild 1, är det därför statistiskt troligt att de virtuella datorerna kan mappas till två feldomäner i stället för fyra.

**Belastningsutjämna belastningsutjämning Thrift trafik:** Thrift klientbibliotek inuti webbservern ansluta till klustret via en intern belastningsutjämnare. Detta kräver att processen att lägga till den interna belastningsutjämnaren ”data-undernätet (se bild 1) i samband med Molntjänsten värd Cassandra klustret. När den interna belastningsutjämnaren har definierats, kräver varje nod belastningsutjämnade-slutpunkt som ska läggas till med anteckningar av en belastningsutjämnad uppsättning med tidigare definierad belastningsutjämnarens namn. Se [Azure intern belastningsutjämning ](../../../load-balancer/load-balancer-internal-overview.md)för mer information.

**Klustret frö:** är det viktigt att välja de flesta högtillgänglig noderna för frö när nya noder kommunicerar med seed nod för att identifiera topologin för klustret. En nod från varje tillgänglighetsuppsättning betecknas som seed noder för att undvika enskild felpunkt.

**Replikering faktor och konsekvensnivå:** Cassandras inbyggda hög tillgänglighet och data hållbarhet kännetecknas av replikering faktor (RF - antal kopior av varje rad som lagras på klustret) och konsekvensnivå (antal replikerna att läsa/skriva innan resultatet returneras till anroparen). Replikering faktor anges under genereringen KEYSPACE (liknar en relationsdatabas) medan konsekvensnivå anges vid utfärdande CRUD-frågan. Se Cassandra dokumentation på [konfigurera konsekvent](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) konsekvenskontroll information och formel för beräkning av kvorum.

Cassandra stöder två typer av integritet datamodeller – konsekvens och slutliga konsekvensen; Replikering faktor och konsekvensnivå avgör tillsammans om data blir konsekvent som en skrivåtgärd är klar eller att det blir överensstämmelse. Till exempel ange KVORUM som nivån konsekvenskontroll kommer alltid garanterar data konsekvenskontroll när alla konsekvensnivå, nedan antal repliker som ska skrivas efter behov för att uppnå KVORUM (t.ex. en) resulterar i att överensstämmelse data.

8 noder klustret ovan med en faktor för replikering på 3 och KVORUM (2 noder läsas eller skrivas konsekvent) läsning och skrivning konsekvensnivå, kan överleva teoretisk förlust av högst 1 nod per replikeringsgrupp innan programmet startas meddela den ett fel uppstod. Detta förutsätter att alla nyckel blanksteg väl har belastningsutjämnade läsning och skrivning begäranden.  Följande är de parametrar som vi ska använda för distribuerade klustret:

Enskild region Cassandra klusterkonfiguration:

| Kluster-Parameter | Värde | Kommentarer |
| --- | --- | --- |
| Antalet noder (N) |8 |Totalt antal noder i klustret |
| Replikering faktor (RF) |3 |Antal repliker på en viss rad |
| Konsekvensnivå (skriva) |QUORUM[(RF/2) +1) = 2] är resultatet av formeln avrundas nedåt |Skriver högst 2 repliker innan svaret skickas till anroparens 3 replik är skriven i ett överensstämmelse sätt. |
| Konsekvensnivå (läsa) |KVORUM [(RF/2) + 1 = 2] resultatet av formeln avrundas nedåt |Läser 2 repliker innan du skickar svar till anroparen. |
| Replikeringsstrategi |NetworkTopologyStrategy Se [datareplikering](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) i Cassandra dokumentationen för mer information |Förstår topologi för distribution och placerar repliker på noder så att alla repliker inte slutar på samma rack. |
| Snitch |GossipingPropertyFileSnitch Se [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) i Cassandra dokumentationen för mer information |NetworkTopologyStrategy använder ett koncept för snitch för att förstå topologin. GossipingPropertyFileSnitch ger bättre kontroll i varje nod att mappa till datacenter och rack. Klustret använder sedan ett för att sprida informationen. Detta är mycket enklare i dynamisk IP-inställningen i förhållande till PropertyFileSnitch |

**Azure överväganden för Cassandra kluster:** kapaciteten för Microsoft Azure-datorer använder Azure Blob storage för disk; Azure Storage sparar 3 repliker för varje disk för hög hållbarhet. Det innebär att varje rad med data som infogas i en tabell med Cassandra lagras redan i 3 repliker och därför datakonsekvens är redan tagit hand om även om replikering faktor (RF) är 1. Det huvudsakliga problemet med replikering faktor 1 är att programmet får driftstopp, även om en enskild nod Cassandra misslyckas. Om en nod avser problem (t.ex. maskinvara, programvara systemfel) som identifieras av Azure-Infrastrukturkontrollanten, kommer den etablera en ny nod i stället använda samma lagringsenheter. Etablera en ny nod för att ersätta det gamla kan ta några minuter.  På samma sätt för planerat underhållsaktiviteter som gäst-OS ändringar Cassandra uppgraderar och ändringar i programmet Azure-Infrastrukturkontrollanten utför rullande uppgraderingar av noderna i klustret.  Även rullande uppgraderingar kan ta bort några noder samtidigt och därför klustret kan ha kort driftstopp för några partitioner. Dock går data inte förlorade på grund av den inbyggda Azure Storage-redundansen.  

För datorer som distribueras till Azure som inte kräver hög tillgänglighet (t.ex. runt 99,9 som motsvarar 8.76 timmar per år, se [hög tillgänglighet](http://en.wikipedia.org/wiki/High_availability) information) du kan köra med RF = 1 och konsekvensnivå = en.  För program med hög tillgänglighet, RF = 3 och konsekvensnivå = KVORUM tolererar nertid av en av noderna och en av replikerna. RF = 1 i traditionella distributioner (t.ex. på plats) kan inte användas på grund av att det uppstår problem angående diskfel förlust av data.   

## <a name="multi-region-deployment"></a>Distribution av flera regioner
Cassandra's data-center-medveten replikering och konsekvent modell ovan hjälper dig med distributionen av flera regioner direkt utan att behöva några externa verktygsuppsättning. Detta är ganska olika från traditionella relationsdatabaser där installationsprogrammet för databasspegling för multimaster skrivningar kan vara ganska komplicerat. Cassandra i en flera region konfigurera kan hjälpa dig med Användningsscenarier inklusive följande:

**Närhet baserat distribution:** program med flera klienter, med Rensa koppling av användare för klient-till-region, kan vara fått av flera regioner klustrets låg latens. Till exempel en learning management system för utbildningssyfte kan distribuera ett kluster som är distribuerade i östra USA och västra USA ska fungera respektive universitet för transaktionell samt analytics. Data kan vara lokalt konsekvent vid den tiden läsningar och skrivningar och kan vara överensstämmelse mellan de båda regionerna. Det finns andra exempel som Mediedistribution, e-handel och något och allt som fungerar geo koncentrerad användaren grundläggande är ett bra användningsfall för denna distributionsmodell.

**Hög tillgänglighet:** redundans är en nyckelfaktor för att uppnå hög tillgänglighet av programvara och maskinvara, se Skapa tillförlitliga Molnsystem på Microsoft Azure för ytterligare information. På Microsoft Azure är endast tillförlitligt sätt att uppnå true redundans genom att distribuera ett kluster med flera regioner. Program kan distribueras i en aktiv-aktiv eller aktivt-passivt läge och om någon av regionerna är nere kan Azure Traffic Manager kan omdirigera trafik till den aktiva regionen.  Med enskild region-distributionen om tillgängligheten är 99,9, en två-region-distribution kan uppnå tillgänglighet 99.9999 beräknas genom följande formel: (1-(1-0.999) * (1-0,999)) * 100); finns i ovanstående information.

**Katastrofåterställning:** flera regioner Cassandra klustret om korrekt utformad klarar oåterkalleligt data center avbrott. Om en region är nere, starta program som distribueras till andra regioner betjänar slutanvändarna. Programmet måste vara feltoleranta för dataförluster uppstår till följd av data i pipeline för asynkron som alla andra business continuity implementeringar. Dock gör Cassandra återställningen mycket snabbare än den tid som traditionella processer. Bild 2 visar en typisk distribution i flera regioner modellen med åtta noder i varje region. Båda regioner är speglade bilder från varandra för samma symmetriplan; Verkligt Designer beror på Arbetsbelastningstyp (t.ex. transaktionell eller analytiska), Återställningspunktsmål, RTO, datakonsekvens och tillgänglighet.

![Distribution av flera region](./media/cassandra-nodejs/cassandra-linux2.png)

Figur 2: Distribution av flera regioner Cassandra

### <a name="network-integration"></a>Integrering av nätverk
Uppsättningar av virtuella datorer distribueras på privata nätverk som finns på områdena kommunicerar med varandra med hjälp av en VPN-tunnel. VPN-tunnel ansluter två programvara gateways etablerats under distribueringen av nätverket. Båda områden har liknande nätverksarkitektur vad gäller ”web” och ”data” undernät; Azure-nätverk kan skapa så många undernät efter behov och tillämpas ACL: er som krävs av nätverkssäkerhet. Vid utformning av klustertopologi bland måste data center svarstiden för kommunikationen och ekonomiska av nätverkstrafiken beaktas.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Datakonsekvens för distribution av flera Datacenter
Distribuerad distributioner måste vara medveten om klustret topologi påverkan på genomflöde och hög tillgänglighet. RF och konsekvensnivå måste väljas så att kvorum inte beroende av tillgängligheten för alla datacenter.
För ett system som kräver hög konsekvens, kontrollerar en LOCAL_QUORUM för konsekvensnivå (för läsning och skrivning) att att lokala läsningar och skrivningar uppfylls från lokala noder medan data replikeras asynkront till fjärr-datacenter.  Tabell 2 sammanfattar konfigurationsinformation för flera regioner klustret beskrivs senare i skrivning upp.

**Två region Cassandra klusterkonfigurationen**

| Kluster-Parameter | Värde | Kommentarer |
| --- | --- | --- |
| Antalet noder (N) |8 + 8 |Totalt antal noder i klustret |
| Replikering faktor (RF) |3 |Antal repliker på en viss rad |
| Konsekvensnivå (skriva) |LOCAL_QUORUM [(sum(RF)/2) +1) = 4] resultatet av formeln avrundas nedåt |2 noder ska skrivas till första datacentret synkront; ytterligare 2 noder som behövs för kvorum skrivs asynkront till 2 datacentret. |
| Konsekvensnivå (läsa) |LOCAL_QUORUM ((RF/2) + 1) = 2 resultatet av formeln avrundas nedåt |Läs begäranden uppfylls från en enda region. 2 noder är skrivskyddade innan svaret skickas tillbaka till klienten. |
| Replikeringsstrategi |NetworkTopologyStrategy Se [datareplikering](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) i Cassandra dokumentationen för mer information |Förstår topologi för distribution och placerar repliker på noder så att alla repliker inte slutar på samma rack. |
| Snitch |GossipingPropertyFileSnitch Se [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) i Cassandra dokumentationen för mer information |NetworkTopologyStrategy använder ett koncept för snitch för att förstå topologin. GossipingPropertyFileSnitch ger bättre kontroll i varje nod att mappa till datacenter och rack. Klustret använder sedan ett för att sprida informationen. Detta är mycket enklare i dynamisk IP-inställningen i förhållande till PropertyFileSnitch |

## <a name="the-software-configuration"></a>KONFIGURATION AV PROGRAMVARA
Följande programvaruversioner används under distributionen:

<table>
<tr><th>Programvara</th><th>Källa</th><th>Version</th></tr>
<tr><td>JRE    </td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA    </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu    </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Hämtning av JRE kräver manuell godkännande av Oracle-licens, för att förenkla distributionen, ladda ned alla nödvändiga program på skrivbordet för senare överföring till Ubuntu-mallavbildningen som vi ska skapa som en ozonbildande till kluster-distributionen.

Ladda ned programmen ovan i en välkänd download katalog (t.ex. %TEMP%/downloads i Windows eller ~/Downloads på de flesta distributioner av Linux- eller Mac) på den lokala datorn.

### <a name="create-ubuntu-vm"></a>SKAPA VIRTUELL UBUNTU-DATOR
I det här steget i processen skapar vi Ubuntu avbildningen med programvara som krävs så att bilden kan återanvändas för att etablera flera Cassandra noder.  

#### <a name="step-1-generate-ssh-key-pair"></a>STEG 1: Skapa SSH-nyckel
Azure behöver X509 offentlig nyckel som är PEM eller DER-kodade då etablering. Generera en offentliga/privata nyckelpar med instruktioner på hur du använda SSH med Linux på Azure. Om du planerar att använda putty.exe som en SSH-klienten på Windows eller Linux måste du konvertera PEM-kodade RSA den privata nyckeln PPK format med hjälp av puttygen.exe; instruktioner för detta finns på webbsidan som ovan.

#### <a name="step-2-create-ubuntu-template-vm"></a>STEG 2: Skapa Ubuntu VM-mallen
Logga in på Azure portal för att skapa VM-mallen, och använder följande sekvens: Klicka på ny, beräkning, VIRTUELLA, från GALLERIET, UBUNTU, Ubuntu Server 14.04 LTS och klicka på pilen till höger. En genomgång som beskriver hur du skapar en Linux VM, se Skapa en virtuell dator kör Linux.

Ange följande information på skärmen ”konfiguration av virtuell dator” #1:

<table>
<tr><th>FÄLTNAMN              </td><td>       FÄLTVÄRDE               </td><td>         KOMMENTARER                </td><tr>
<tr><td>VERSION UTGIVNINGSDATUM    </td><td> Välj ett datum i nedrullningsbara ned</td><td></td><tr>
<tr><td>NAMN PÅ VIRTUELL DATOR    </td><td> fråga mall                   </td><td> Detta är värdnamnet på den virtuella datorn </td><tr>
<tr><td>NIVÅ                     </td><td> STANDARD                           </td><td> Låt standardvärdet              </td><tr>
<tr><td>STORLEK                     </td><td> A1                              </td><td>Välj den virtuella datorn baserat på i/o-behov. Låt standardvärdet för detta ändamål </td><tr>
<tr><td> NYTT ANVÄNDARNAMN             </td><td> localadmin                       </td><td> ”admin” är ett reserverat användarnamn i Ubuntu 12. xx och efter</td><tr>
<tr><td> AUTENTISERING         </td><td> Markera kryssrutan                 </td><td>Markera om du vill skydda med en SSH-nyckel </td><tr>
<tr><td> CERTIFIKAT             </td><td> filnamnet på det offentliga nyckelcertifikatet </td><td> Använd den offentliga nyckeln som skapats tidigare</td><tr>
<tr><td> Nytt lösenord    </td><td> starkt lösenord </td><td> </td><tr>
<tr><td> Bekräfta lösenord    </td><td> starkt lösenord </td><td></td><tr>
</table>

Ange följande information på skärmen ”konfiguration av virtuell dator” #2:

<table>
<tr><th>FÄLTNAMN             </th><th> FÄLTVÄRDE                       </th><th> KOMMENTARER                                 </th></tr>
<tr><td> MOLNTJÄNSTEN    </td><td> Skapa en ny molntjänst    </td><td>Molntjänst är en behållare beräkningsresurser som virtuella datorer</td></tr>
<tr><td> DNS-MOLNTJÄNSTNAMNET    </td><td>Ubuntu template.cloudapp.net    </td><td>Ge en datorn storleksoberoende belastningsutjämnarens namn</td></tr>
<tr><td> REGION/TILLHÖRIGHETSGRUPP/VIRTUELLT NÄTVERK </td><td>    Västra USA    </td><td> Välj en region som ditt webbprogram till Cassandra klustret</td></tr>
<tr><td>LAGRINGSKONTO </td><td>    Använd standard    </td><td>Använda standardkontot för lagring eller ett befintligt lagringskonto i en viss region</td></tr>
<tr><td>TILLGÄNGLIGHETSUPPSÄTTNING </td><td>    Ingen </td><td>    Lämna det tomt</td></tr>
<tr><td>SLUTPUNKTER    </td><td>Använd standard </td><td>    Använd standardkonfigurationen för SSH </td></tr>
</table>

Klicka på högerpilen och lämna standardvärdena på skärmen #3 knappen ”Kontrollera” för att slutföra etableringsprocessen VM. Den virtuella datorn med namnet ”ubuntu-mall” måste vara i statusen ”körs” efter några minuter.

### <a name="install-the-necessary-software"></a>INSTALLERA NÖDVÄNDIG PROGRAMVARA
#### <a name="step-1-upload-tarballs"></a>STEG 1: Ladda upp tarballs
Använder scp eller pscp, kopierar du tidigare hämtade programvaran till ~/downloads katalogen med hjälp av följande kommandoformat:

##### <a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp server-jre-8u5-linux-x64.tar.gzlocaladmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz
Upprepa ovanstående kommando för JRE samt som Cassandra bitar.

#### <a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>STEG 2: Förbered katalogstrukturen och extrahera Arkiv
Logga in på den virtuella datorn och skapa katalogstrukturen och extrahera programvara som en superanvändare använder bash-skriptet nedan:

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


Om du klistrar in det här skriptet i vim fönster måste du ta bort radmatningen ('\r ”) med följande kommando:

    tr -d '\r' <infile.sh >outfile.sh

#### <a name="step-3-edit-etcprofile"></a>Steg 3: Redigera etc-profil
Lägg till följande i slutet:

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

#### <a name="step-4-install-jna-for-production-systems"></a>Steg 4: Installera JNA för produktionssystem
Använd följande kommandosekvens: följande kommando kommer installerar jna 3.2.7.jar och jna plattform 3.2.7.jar till /usr/share.java directory lgh sudo-get libjna java

Skapa symboliska länkar i $CASS_HOME/lib directory så att Cassandra startskript kan hitta dessa burkar:

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

#### <a name="step-5-configure-cassandrayaml"></a>Steg 5: Konfigurera cassandra.yaml
Redigera cassandra.yaml på varje virtuell konfiguration som krävs av alla virtuella datorer [vi ska justera detta under den faktiska etableringen]:

<table>
<tr><th>Fältnamn   </th><th> Värde  </th><th>    Kommentarer </th></tr>
<tr><td>klusternamn </td><td>    ”CustomerService”    </td><td> Använda namn som motsvarar din distribution</td></tr>
<tr><td>listen_address    </td><td>[lämna det tomt]    </td><td> Ta bort ”localhost” </td></tr>
<tr><td>rpc_addres   </td><td>[lämna det tomt]    </td><td> Ta bort ”localhost” </td></tr>
<tr><td>frö    </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"    </td><td>Lista över alla IP-adresser som är avsedda som frö.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Detta används av NetworkTopologyStrateg för procedurens datacenter och rack av den virtuella datorn</td></tr>
</table>

#### <a name="step-6-capture-the-vm-image"></a>Steg 6: Hämta VM-avbildning
Logga in på den virtuella datorn med värdnamn (hk-cas-template.cloudapp.net) och privata SSH-nyckeln skapade tidigare. Se hur du använda SSH med Linux i Azure för information om hur du loggar in med hjälp av kommandot ssh eller putty.exe.

Kör följande sekvens med åtgärder för att avbilda:

##### <a name="1-deprovision"></a>1. Avetablering
Använd kommandot ”sudo waagent – avetablering + användare” ta bort virtuella instansen specifik information. Se för [så här skapar du en virtuell Linux-dator](capture-image.md) ska användas som en mall för mer information på image-hämtningen.

##### <a name="2-shutdown-the-vm"></a>2: Stäng den virtuella datorn
Kontrollera att den virtuella datorn har markerats och klicka på länken avstängning från kommandofältet längst ned.

##### <a name="3-capture-the-image"></a>3: spara avbildningen
Kontrollera att den virtuella datorn har markerats och klicka på länken avbildning från kommandofältet längst ned. På nästa skärm namnge en bild (t.ex. hk-cas-2-08-ub-14-04-2014071), lämpliga AVBILDNINGSBESKRIVNINGEN och klicka på ”kryssmarkeringen” om du vill slutföra bildtagningen.

Detta tar några sekunder och avbildningen ska vara tillgänglig under Mina AVBILDNINGAR i galleriet för avbildningen. Den Virtuella källdatorn tas automatiskt bort när avbildningen har gjorts. 

## <a name="single-region-deployment-process"></a>Processen för distribution av enskild Region
**Steg 1: Skapa det virtuella nätverket** logga in på Azure-portalen och skapa ett virtuellt nätverk (klassiskt) med attribut som visas i följande tabell. Se [skapa ett virtuellt nätverk (klassiska) med hjälp av Azure portal](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för detaljerade steg i processen.      

<table>
<tr><th>VM attributets namn</th><th>Värde</th><th>Kommentarer</th></tr>
<tr><td>Namn</td><td>vnet-fråga-Väst-oss</td><td></td></tr>
<tr><td>Region</td><td>Västra USA</td><td></td></tr>
<tr><td>DNS-servrar</td><td>Ingen</td><td>Ignorera detta eftersom vi inte använder en DNS-Server</td></tr>
<tr><td>Adressutrymme</td><td>10.1.0.0/16</td><td></td></tr>    
<tr><td>Starta IP</td><td>10.1.0.0</td><td></td></tr>    
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

Lägg till följande undernät:

<table>
<tr><th>Namn</th><th>Starta IP</th><th>CIDR</th><th>Kommentarer</th></tr>
<tr><td>webben</td><td>10.1.1.0</td><td>/24 (251)</td><td>Undernät för webbservergrupp</td></tr>
<tr><td>data</td><td>10.1.2.0</td><td>/24 (251)</td><td>Undernät för databasnoder</td></tr>
</table>

Data och Web undernät kan skyddas via nätverkssäkerhetsgrupper täckning som ligger utanför omfånget för den här artikeln.  

**Steg 2: Etablera virtuella datorer** med den avbildning som har skapats tidigare vi skapar följande virtuella datorer i molnet server ”hk-c-svc-Väst” och binda dem till respektive undernät som visas nedan:

<table>
<tr><th>Datornamn    </th><th>Undernät    </th><th>IP-adress    </th><th>Tillgänglighetsuppsättning</th><th>DC/Rack</th><th>Startvärde för?</th></tr>
<tr><td>HK-c1-Väst-oss    </td><td>data    </td><td>10.1.2.4    </td><td>HK-c-aset-1    </td><td>DC = WESTUS rack = rack1 </td><td>Ja</td></tr>
<tr><td>HK-c2-Väst-oss    </td><td>data    </td><td>10.1.2.5    </td><td>HK-c-aset-1    </td><td>DC = WESTUS rack = rack1    </td><td>Nej </td></tr>
<tr><td>HK-c3-Väst-oss    </td><td>data    </td><td>10.1.2.6    </td><td>HK-c-aset-1    </td><td>DC = WESTUS rack = rack2    </td><td>Ja</td></tr>
<tr><td>HK-c4-Väst-oss    </td><td>data    </td><td>10.1.2.7    </td><td>HK-c-aset-1    </td><td>DC = WESTUS rack = rack2    </td><td>Nej </td></tr>
<tr><td>HK-c5-Väst-oss    </td><td>data    </td><td>10.1.2.8    </td><td>HK-c-aset-2    </td><td>DC = WESTUS rack = rack3    </td><td>Ja</td></tr>
<tr><td>HK-c6-Väst-oss    </td><td>data    </td><td>10.1.2.9    </td><td>HK-c-aset-2    </td><td>DC = WESTUS rack = rack3    </td><td>Nej </td></tr>
<tr><td>HK-c7-Väst-oss    </td><td>data    </td><td>10.1.2.10    </td><td>HK-c-aset-2    </td><td>DC = WESTUS rack = rack4    </td><td>Ja</td></tr>
<tr><td>HK-c8-Väst-oss    </td><td>data    </td><td>10.1.2.11    </td><td>HK-c-aset-2    </td><td>DC = WESTUS rack = rack4    </td><td>Nej </td></tr>
<tr><td>HK-w1-Väst-oss    </td><td>webben    </td><td>10.1.1.4    </td><td>HK-w-aset-1    </td><td>                       </td><td>Saknas</td></tr>
<tr><td>HK-w2-Väst-oss    </td><td>webben    </td><td>10.1.1.5    </td><td>HK-w-aset-1    </td><td>                       </td><td>Saknas</td></tr>
</table>

Skapa listan ovan för virtuella datorer kräver följande process:

1. Skapa en tom molntjänst i ett visst område
2. Skapa en virtuell dator från den tidigare avbildningen och koppla den till det virtuella nätverket som skapats tidigare; Upprepa detta för alla virtuella datorer
3. Lägg till en intern belastningsutjämnare till Molntjänsten och koppla den till undernätet ”data”
4. Lägg till en slutpunkt för Utjämning av nätverksbelastning för thrift-trafik via en belastningsutjämnad uppsättning som är anslutna till den tidigare skapade interna belastningsutjämnaren för varje VM som skapats tidigare

Ovanstående procedur kan utföras med hjälp av Azure klassiska portal; Använd Windows-dator (Använd en virtuell dator i Azure om du inte har åtkomst till en Windows-dator), använda följande PowerShell-skript för att etablera alla 8 virtuella datorer automatiskt.

**Lista över 1: PowerShell-skript för etablering av virtuella datorer**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template

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
        #Add add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**Steg 3: Konfigurera Cassandra på varje virtuell dator**

Logga in på den virtuella datorn och utför följande:

* Redigera $CASS_HOME/conf/cassandra-rackdc.properties om du vill ange egenskaper för data center och rack:
  
       dc =EASTUS, rack =rack1
* Redigera cassandra.yaml om du vill konfigurera seed noder enligt nedan:
  
       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Steg 4: Starta de virtuella datorerna och testar klustret**

Logga in på en av noderna (t.ex. hk-c1-Väst-us) och kör följande kommando för att se status för klustret:

       nodetool –h 10.1.2.4 –p 7199 status

Du bör se visningen liknar den nedan för ett kluster på 8 noder:

<table>
<tr><th>Status</th><th>Adress    </th><th>Läsa in    </th><th>Token    </th><th>Äger </th><th>Värd-ID    </th><th>Rack</th></tr>
<tr><th>AVREGISTRERAR    </td><td>10.1.2.4     </td><td>87.81 KB    </td><td>256    </td><td>38.0%    </td><td>GUID (tas bort)</td><td>rack1</td></tr>
<tr><th>AVREGISTRERAR    </td><td>10.1.2.5     </td><td>41.08 KB    </td><td>256    </td><td>68.9%    </td><td>GUID (tas bort)</td><td>rack1</td></tr>
<tr><th>AVREGISTRERAR    </td><td>10.1.2.6     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (tas bort)</td><td>rack2</td></tr>
<tr><th>AVREGISTRERAR    </td><td>10.1.2.7     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (tas bort)</td><td>rack2</td></tr>
<tr><th>AVREGISTRERAR    </td><td>10.1.2.8     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (tas bort)</td><td>rack3</td></tr>
<tr><th>AVREGISTRERAR    </td><td>10.1.2.9     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (tas bort)</td><td>rack3</td></tr>
<tr><th>AVREGISTRERAR    </td><td>10.1.2.10     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (tas bort)</td><td>rack4</td></tr>
<tr><th>AVREGISTRERAR    </td><td>10.1.2.11     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (tas bort)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Testa det enda Region-klustret
Använd följande steg för att testa klustret:

1. Med Powershell-kommandot Get-AzureInternalLoadbalancer-kommandot, hämta IP-adressen för den interna belastningsutjämnaren (t.ex.)  10.1.2.101). Syntaxen för kommandot visas nedan: Get-AzureLoadbalancer – ServiceName ”hk-c-svc-Väst-oss” [visar information om den interna belastningsutjämnaren tillsammans med dess IP-adress]
2. Logga in på webbservergrupp VM (t.ex. hk-w1-Väst-us) med hjälp av Putty eller ssh
3. Köra $CASS_HOME/bin/cqlsh 10.1.2.101 9160
4. Använd följande CQL kommandon för att kontrollera om klustret fungerar:
   
     Skapa KEYSPACE customers_ks med replikering = {'class': 'SimpleStrategy', 'replication_factor': 3};   ANVÄNDA customers_ks;   Skapa tabell Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Infoga i Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Infoga i Customers(customer_id, firstname, lastname) värden (2, ”Jane', 'Berg”).
   
     Välj * från kunder.

Du bör se en skärm som liknar den nedan:

<table>
  <tr><th> customer_id </th><th> Förnamn </th><th> Efternamn </th></tr>
  <tr><td> 1 </td><td> John </td><td> Doe </td></tr>
  <tr><td> 2 </td><td> Jane </td><td> Doe </td></tr>
</table>

Observera att keyspace skapade i steg 4 använder SimpleStrategy med en replication_factor 3. SimpleStrategy rekommenderas för enda data center distributioner medan NetworkTopologyStrategy för flera data center distributioner. En replication_factor 3 ger tolerans för nodfel.

## <a id="tworegion"></a>Flera regioner distributionsprocessen
Utnyttja den enda region distributionen har slutförts och upprepa samma steg för att installera andra region. Den viktigaste skillnaden mellan enstaka och flera region distributionen är VPN-tunnel konfiguration för kommunikation mellan region. Vi börjar med nätverksinstallation, etablera virtuella datorer och konfigurera Cassandra.

### <a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>Steg 1: Skapa virtuella nätverk på 2 Region
Logga in på den klassiska Azure-portalen och skapa ett virtuellt nätverk med attribut visas i tabellen. Se [konfigurera ett virtuellt nätverk Cloud-Only i den klassiska Azure-portalen](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) för detaljerade steg i processen.      

<table>
<tr><th>Attributnamn    </th><th>Värde    </th><th>Kommentarer</th></tr>
<tr><td>Namn    </td><td>vnet-fråga-Öst-oss</td><td></td></tr>
<tr><td>Region    </td><td>Östra USA</td><td></td></tr>
<tr><td>DNS-servrar        </td><td></td><td>Ignorera detta eftersom vi inte använder en DNS-Server</td></tr>
<tr><td>Konfigurera en punkt-till-plats-VPN</td><td></td><td>        Ignorera detta</td></tr>
<tr><td>Konfigurera ett VPN mellan två platser</td><td></td><td>        Ignorera detta</td></tr>
<tr><td>Adressutrymme    </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>Starta IP    </td><td>10.2.0.0    </td><td></td></tr>
<tr><td>CIDR    </td><td>/16 (65531)</td><td></td></tr>
</table>

Lägg till följande undernät:

<table>
<tr><th>Namn    </th><th>Starta IP    </th><th>CIDR    </th><th>Kommentarer</th></tr>
<tr><td>webben    </td><td>10.2.1.0    </td><td>/24 (251)    </td><td>Undernät för webbservergrupp</td></tr>
<tr><td>data    </td><td>10.2.2.0    </td><td>/24 (251)    </td><td>Undernät för databasnoder</td></tr>
</table>


### <a name="step-2-create-local-networks"></a>Steg 2: Skapa lokala nätverk
Ett lokalt nätverk i Azure virtuella nätverk är en proxy-adressutrymme som mappar till en fjärrplats, inklusive ett privat moln eller en annan Azure-region. Det här adressutrymmet för proxy är bunden till en fjärr-gateway för routning nätverket till rätt nätverk mål. Se [konfigurera ett virtuellt nätverk för virtuella nätverk anslutningen](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) anvisningar för VNET-till-VNET-anslutning upprättas.

Skapa två lokala nätverk per följande information:

| Nätverksnamn | VPN Gateway-adress | Adressutrymme | Kommentarer |
| --- | --- | --- | --- |
| HK-lnet-Map-to-East-US |23.1.1.1 |10.2.0.0/16 |När du skapar ge det lokala nätverket en platshållare för gateway-adress. Verklig gateway-adressen är fylld när gatewayen har skapats. Se till att adressutrymmet exakt matchar respektive Fjärrnätverket; i det här fallet skapas VNET i östra USA. |
| HK-lnet-Map-to-West-US |23.2.2.2 |10.1.0.0/16 |När du skapar ge det lokala nätverket en platshållare för gateway-adress. Verklig gateway-adressen är fylld när gatewayen har skapats. Se till att adressutrymmet exakt matchar respektive Fjärrnätverket; i det här fallet skapas VNET i USA, västra region. |

### <a name="step-3-map-local-network-to-the-respective-vnets"></a>Steg 3: Karta ”lokalt” nätverk till respektive Vnet
Markera varje virtuellt nätverk från den klassiska Azure-portalen, klickar du på ”Konfigurera”, kontrollera ”ansluta till det lokala nätverket” och väljer du de lokala nätverk per följande information:

| Virtual Network | Lokalt nätverk |
| --- | --- |
| HK-vnet-Väst-oss |HK-lnet-Map-to-East-US |
| HK-vnet-Öst-oss |HK-lnet-Map-to-West-US |

### <a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>Steg 4: Skapa Gateways på VNET1 och VNET2
Klicka på Skapa GATEWAYEN som utlöser VPN-gatewayen etableringsprocessen från instrumentpanelen för virtuella nätverk. Efter några minuter visas på instrumentpanelen i varje virtuellt nätverk bör faktiska gateway-adress.

### <a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>Steg 5: Uppdatera ”lokala” nätverk med respektive ”Gateway”-adresser
Redigera både de lokala nätverk för att ersätta platshållare gateway IP-adress med bara etablerad gateway verkliga IP-adress. Använd följande mappning:

<table>
<tr><th>Lokalt nätverk    </th><th>Virtuell nätverksgateway</th></tr>
<tr><td>HK-lnet-Map-to-East-US </td><td>Gateway för hk-vnet-Väst-oss</td></tr>
<tr><td>HK-lnet-Map-to-West-US </td><td>Gateway för hk-vnet-Öst-oss</td></tr>
</table>

### <a name="step-6-update-the-shared-key"></a>Steg 6: Uppdatera delad nyckel
Använd följande Powershell-skript för att uppdatera IPSec-nyckel för varje VPN-gateway [Använd saké nyckel för både gateway]: Set-AzureVNetGatewayKey - VNetName hk-vnet-Öst-oss - LocalNetworkSiteName hk-lnet-map-to-west-us - SharedKey D9E76BKK Set-AzureVNetGatewayKey - VNetName hk-vnet-Väst-oss - LocalNetworkSiteName hk-lnet-map-to-east-us - SharedKey D9E76BKK

### <a name="step-7-establish-the-vnet-to-vnet-connection"></a>Steg 7: Skapa VNET-till-VNET-anslutning
Använd menyn ”INSTRUMENTPANELEN” virtuella nätverk från den klassiska Azure-portalen för att upprätta en anslutning för gateway-till-gateway. Använd ”Anslut” menyalternativ i det nedersta verktygsfältet. Efter några minuter ska instrumentpanelen visa anslutningsinformationen grafiskt.

### <a name="step-8-create-the-virtual-machines-in-region-2"></a>Steg 8: Skapa de virtuella datorerna i området #2
Skapa avbildningen Ubuntu enligt beskrivningen i region #1 distribution genom att följa samma steg eller kopiera avbildningen VHD-filen till Azure storage-konto finns i området #2 och skapa avbildningen. Använd den här avbildningen och skapa följande lista över virtuella datorer till en ny molntjänst hk-c-svc-Öst-oss:

| Datornamn | Undernät | IP-adress | Tillgänglighetsuppsättning | DC/Rack | Startvärde för? |
| --- | --- | --- | --- | --- | --- |
| HK-c1-Öst-oss |data |10.2.2.4 |HK-c-aset-1 |DC = EASTUS rack = rack1 |Ja |
| HK-c2-Öst-oss |data |10.2.2.5 |HK-c-aset-1 |DC = EASTUS rack = rack1 |Nej |
| HK-c3-Öst-oss |data |10.2.2.6 |HK-c-aset-1 |DC = EASTUS rack = rack2 |Ja |
| HK-c5-Öst-oss |data |10.2.2.8 |HK-c-aset-2 |DC = EASTUS rack = rack3 |Ja |
| HK-c6-Öst-oss |data |10.2.2.9 |HK-c-aset-2 |DC = EASTUS rack = rack3 |Nej |
| HK-c7-Öst-oss |data |10.2.2.10 |HK-c-aset-2 |DC = EASTUS rack = rack4 |Ja |
| HK-c8-Öst-oss |data |10.2.2.11 |HK-c-aset-2 |DC = EASTUS rack = rack4 |Nej |
| HK-w1-Öst-oss |webben |10.2.1.4 |HK-w-aset-1 |Saknas |Saknas |
| HK-w2-Öst-oss |webben |10.2.1.5 |HK-w-aset-1 |Saknas |Saknas |

Följ samma anvisningar som region #1 men använda 10.2.xxx.xxx adressutrymme.

### <a name="step-9-configure-cassandra-on-each-vm"></a>Steg 9: Konfigurera Cassandra på varje virtuell dator
Logga in på den virtuella datorn och utför följande:

1. Redigera $CASS_HOME/conf/cassandra-rackdc.properties för att ange egenskaper för data center och rack i formatet: dc = EASTUS rack = rack1
2. Redigera cassandra.yaml om du vill konfigurera seed noder: frö: ”10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10”

### <a name="step-10-start-cassandra"></a>Steg 10: Starta Cassandra
Logga in på varje virtuell dator och starta Cassandra i bakgrunden genom att köra följande kommando: $CASS_HOME/bin/cassandra

## <a name="test-the-multi-region-cluster"></a>Testa flera regioner klustret
Nu har Cassandra distribuerats till 16 noder med 8 noder i varje Azure-region. Dessa noder finns i samma kluster tack vare vanliga klusternamnet och nodkonfiguration startvärde. Du kan använda följande process för att testa klustret:

### <a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>Steg 1: Hämta den intern belastningsutjämnaren IP-Adressen för båda regioner med hjälp av PowerShell
* Get-AzureInternalLoadbalancer - ServiceName ”hk-c-svc-Väst-oss”
* Get-AzureInternalLoadbalancer - ServiceName ”hk-c-svc-Öst-oss”  
  
    Observera de IP-adresserna (t.ex. west - 10.1.2.101, Öst - 10.2.2.101) visas.

### <a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>Steg 2: Kör följande när du har loggat in hk-w1-Väst-oss i region Väst
1. Köra $CASS_HOME/bin/cqlsh 10.1.2.101 9160
2. Kör följande kommandon för CQL:
   
     Skapa KEYSPACE customers_ks med replikering = {'class': 'NetworkToplogyStrategy', 'WESTUS': 3, EASTUS: 3};   ANVÄNDA customers_ks;   Skapa tabell Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Infoga i Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Infoga i Customers(customer_id, firstname, lastname) värden (2, ”Jane', 'Berg”).   Välj * från kunder.

Du bör se en skärm som liknar den nedan:

| customer_id | Förnamn | Efternamn |
| --- | --- | --- |
| 1 |John |Doe |
| 2 |Jane |Doe |

### <a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>Steg 3: Kör du följande i region Öst när du har loggat in hk-w1-Öst-oss:
1. Köra $CASS_HOME/bin/cqlsh 10.2.2.101 9160
2. Kör följande kommandon för CQL:
   
     ANVÄNDA customers_ks;   Skapa tabell Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Infoga i Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Infoga i Customers(customer_id, firstname, lastname) värden (2, ”Jane', 'Berg”).   Välj * från kunder.

Du bör se samma skärm som visas för regionen Väst:

| customer_id | Förnamn | Efternamn |
| --- | --- | --- |
| 1 |John |Doe |
| 2 |Jane |Doe |

Köra några fler infogningar och se att de replikeras till Väst-oss ingår i klustret.

## <a name="test-cassandra-cluster-from-nodejs"></a>Cassandra Testklustret från Node.js
Använder en Linux virtuella datorer som skapats i ”web” tjänstnivån tidigare, vi ska köra en enkel Node.js-skript för att läsa tidigare infogade data

**Steg 1: Installera Node.js och Cassandra-klienten**

1. Installera Node.js och npm
2. Installera nod paketet ”cassandra-klient” med hjälp av npm
3. Kör följande skript i Kommandotolken shell som visar json-strängen för hämtade data:
   
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
   
        //update will also insert the record if none exists
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

## <a name="conclusion"></a>Slutsats
Microsoft Azure är en flexibel plattform som tillåter körning av både Microsoft samt programvara med öppen källkod som visas av den här övningen. Hög tillgänglighet Cassandra kluster kan distribueras på ett enda datacenter genom att sprida klusternoder över flera feldomäner. Cassandra kluster kan också distribueras över flera Azure geografiskt avlägsna regioner för katastrofåterställning bevis system. Azure och Cassandra aktiverar tillsammans konstruktionen av skalbara, hög tillgänglighet och katastrofåterställning återställningsbara molntjänster behövs dagens internet skalas tjänster.  

## <a name="references"></a>Referenser
* [http://cassandra.apache.org](http://cassandra.apache.org)
* [http://www.datastax.com](http://www.datastax.com)
* [http://www.nodejs.org](http://www.nodejs.org)

