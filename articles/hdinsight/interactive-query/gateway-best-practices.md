---
title: Djupdykning och metodtips för Apache Hive i Azure HDInsight
description: Lär dig hur du navigerar i metodtipsen för att köra Hive-frågor via Azure HDInsight-gatewayen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 924b1132efeb3ee4211593da190f5b7251029ae3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586982"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Djupdykning och metodtips för Apache Hive i Azure HDInsight

Azure HDInsight gateway (Gateway) är HTTPS-klientdelen för HDInsight-kluster. Gatewayen ansvarar för: autentisering, värdmatchning, tjänstidentifiering och andra användbara funktioner som krävs för ett modernt distribuerat system. Funktionerna i gatewayen resulterar i vissa omkostnader som det här dokumentet beskriver de bästa metoderna för att navigera. Felsökningsteknik för gatewayer diskuteras också.

## <a name="the-hdinsight-gateway"></a>HDInsight-gatewayen

HDInsight-gatewayen är den enda delen av ett HDInsight-kluster som är allmänt tillgängligt via Internet. Gateway-tjänsten kan nås utan att gå över `clustername-int.azurehdinsight.net` det offentliga internet med hjälp av den interna gateway-slutpunkten. Den interna gateway-slutpunkten gör att anslutningar kan upprättas till gateway-tjänsten utan att avsluta klustrets virtuella nätverk. Gatewayen hanterar alla begäranden som skickas till klustret och vidarebefordrar sådana begäranden till rätt komponenter och klustervärdar.

Följande diagram ger en grov illustration av hur gatewayen ger en abstraktion framför alla olika möjligheter värdupplösning inom HDInsight.

![Diagram över värdupplösning](./media/gateway-best-practices/host-resolution-diagram.png "Diagram över värdupplösning")

## <a name="motivation"></a>Motivation

Motivationen för att placera en gateway framför HDInsight-kluster är att tillhandahålla ett gränssnitt för identifiering av tjänster och användarautentisering. De autentiseringsmekanismer som tillhandahålls av gatewayen är särskilt relevanta för ESP-aktiverade kluster.

För identifiering av tjänster är fördelen med gatewayen att varje komponent i klustret `clustername.azurehdinsight.net/hive2`kan nås som olika `host:port` slutpunkt under gateway-webbplatsen ( ), i motsats till en mängd parningar.

För autentisering tillåter gatewayen användare `username:password` att autentisera med ett autentiseringspar. För ESP-aktiverade kluster är den här autentiseringsidentialen användarens domännamn och lösenord. Autentisering till HDInsight-kluster via gatewayen kräver inte att klienten skaffar en kerberos-biljett. Eftersom gatewayen `username:password` accepterar autentiseringsuppgifter och hämtar användarens Kerberos-biljett för användarens räkning kan säkra anslutningar göras till gatewayen från valfri klientvärd, inklusive klienter som är anslutna till olika AA-DDS-domäner än (ESP)-klustret.

## <a name="best-practices"></a>Bästa praxis

Gatewayen är en enda tjänst (belastningsutjämnad mellan två värdar) som ansvarar för vidarebefordran och autentisering av begäranden. Gatewayen kan bli en flaskhals för Hive-frågor som överskrider en viss storlek. Frågeprestandaförsämring kan observeras när mycket stora **SELECT-frågor** körs på gatewayen via ODBC eller JDBC. "Mycket stor" betyder frågor som utgör mer än 5 GB data över rader eller kolumner. Den här frågan kan innehålla en lång lista med rader och, eller ett stort kolumnantal.

Gatewayens prestandaförsämring runt frågor av stor storlek beror på att data måste överföras från det underliggande datalagret (ADLS Gen2) till: HDInsight Hive Server, Gateway och slutligen via JDBC- eller ODBC-drivrutinerna till klientvärden.

Följande diagram illustrerar stegen i en SELECT-fråga.

![Resultatdiagram](./media/gateway-best-practices/result-retrieval-diagram.png "Resultatdiagram")

Apache Hive är en relationsabstraktion ovanpå ett HDFS-kompatibelt filsystem. Den här abstraktionen innebär att **SELECT-satser** i Hive motsvarar **READ-åtgärder** i filsystemet. **READ-åtgärderna** översätts till lämpligt schema innan de rapporteras till användaren. Svarstiden för den här processen ökar med datastorlek och totalt hopp som krävs för att nå slutanvändaren.

Liknande beteende kan uppstå när **skapa** eller **infoga-satser** för stora data, eftersom dessa kommandon motsvarar **WRITE-åtgärder** i det underliggande filsystemet. Överväg att skriva data, till exempel rå ORC, till filsystemet/datalake i stället för att läsa in dem med **INSERT** eller **LOAD**.

I Clusters-aktiverade Kluster med Enterprise Security Pack kan tillräckligt komplexa Apache Ranger-principer orsaka en avmattning i frågekompileringstiden, vilket kan leda till en timeout för gatewayen. Om en timeout för gateway upptäcks i ett ESP-kluster bör du överväga att minska eller kombinera antalet intervallprinciper.

## <a name="troubleshooting-techniques"></a>Felsökningstekniker

Det finns flera platser för att minska och förstå prestandafrågor som träffades som en del av ovanstående beteende. Använd följande checklista när du upplever att frågan försämras under HDInsight-gatewayen:

* Använd **LIMIT-satsen** när du kör stora **SELECT-frågor.** **LIMIT-satsen** minskar antalet rader som rapporterats till klientvärden. **LIMIT-satsen** påverkar bara resultatgenereringen och ändrar inte frågeplanen. Om du vill tillämpa **LIMIT-satsen** `hive.limit.optimize.enable`på frågeplanen använder du konfigurationen . **LIMIT** kan kombineras med en förskjutning med hjälp av argumentformuläret **LIMIT x,y**.

* Namnge dina intressekolumner när du kör **SELECT-frågor** i stället för att använda ** \*SELECT **. Om du väljer färre kolumner sänks mängden data som läss.

* Prova att köra frågan av intresse via Apache Beeline. Om resultathämtning via Apache Beeline tar lång tid, förvänta dig fördröjningar när du hämtar samma resultat via externa verktyg.

* Testa en grundläggande Hive-fråga för att säkerställa att en anslutning till HDInsight Gateway kan upprättas. Prova att köra en grundläggande fråga från två eller flera externa verktyg för att se till att inget enskilt verktyg körs på problem.

* Granska alla Apache Ambari-aviseringar för att se till att HDInsight-tjänster är felfria. Ambari-aviseringar kan integreras med Azure Monitor för rapportering och analys.

* Om du använder en extern Hive Metastore kontrollerar du att Azure SQL DB DTU för Hive Metastore inte har nått sin gräns. Om DTU närmar sig sin gräns måste du öka databasstorleken.

* Kontrollera att alla verktyg från tredje part som PBI eller Tableau använder sidnumrering för att visa tabeller eller databaser. Kontakta dina supportpartners för dessa verktyg för hjälp med sidnumrering. Huvudverktyget som används för sidnumrering `fetchSize` är JDBC-parametern. En liten hämtningsstorlek kan resultera i försämrad gateway-prestanda, men en hämtningsstorlek som är för stor kan resultera i en timeout för gateway. Hämta storleksjustering måste göras på arbetsbelastningsbasis.

* Om datapipelinen innebär att du läser stora mängder data från HDInsight-klustrets underliggande lagring kan du överväga att använda ett verktyg som samverkar direkt med Azure Storage, till exempel Azure Data Factory

* Överväg att använda Apache Hive LLAP när du kör interaktiva arbetsbelastningar, eftersom LLAP kan ge en smidigare upplevelse för att snabbt returnera frågeresultat

* Överväg att öka antalet trådar som är tillgängliga `hive.server2.thrift.max.worker.threads`för Tjänsten Hive Metastore med . Den här inställningen är särskilt relevant när ett stort antal samtidiga användare skickar frågor till klustret

* Minska antalet försök som används för att nå gatewayen från externa verktyg. Om flera återförsök används kan du överväga att följa en exponentiell bakåtsträvningsprincip

* Överväg att aktivera komprimerings hive med hjälp av konfigurationer `hive.exec.compress.output` och `hive.exec.compress.intermediate`.

## <a name="next-steps"></a>Nästa steg

* [Apache Beeline på HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)
* [Felsökningssteg för HDInsight Gateway-timeout](https://docs.microsoft.com/azure/hdinsight/interactive-query/troubleshoot-gateway-timeout)
* [Virtuella nätverk för HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)
* [HDInsight med Express Route](https://docs.microsoft.com/azure/hdinsight/connect-on-premises-network)